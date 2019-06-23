# Create Image Resizer with Cloud Functions.

## What you can achieve.

You can get resized images which is handled by GCF, the specifications are below.

URL example *https://functionurl/accountId/imageName.png?w=200&h=400&crop=cover*

- set query paramter
  - w: set number of width in pixel
  - h: set number of height in pixel
  - c: set `cover` if you want to crop the image in cropped in the rectangle set by w and h param.
  
## Architecture

1. Upload files manually.
2. Return resized image via functions.

refs: https://github.com/firebase/functions-samples/tree/master/generate-thumbnail

## Build Up

1. create Storage with Firebase console.
1. setup [gcloud sdk](https://cloud.google.com/sdk/docs/).
1. make directory structure.
   ```
   functions/
     - cmd/
       - main.go <= for development.
     - internal/
       - image/
         - image.go
         - query.go
         - rect.go
     - pkg/
       - storage/
         - storage.go
     - function.go <= run in gcp as entrypoint.
     - go.mod

## Source

### function.go

```go
package functions

import (
	"fmt"
	"github.com/wtrdr/watarilog/functions/internal/image"
	"github.com/wtrdr/watarilog/functions/pkg/storage"
	"log"
	"net/http"
	"strings"
)

func init() {
	if err := storage.Init(); err != nil {
		log.Fatalf("storage.Init: %v", err)
	}
}

func Resize(w http.ResponseWriter, r *http.Request) {
	path := strings.TrimLeft(r.URL.Path, "/")
	log.Printf("file '%s' is been handling.", path)

	_r, err := storage.Reader(r.Context(), path)
	if err != nil {
		log.Printf("storage.Reader: %v", err)
		notFound(w, path)
		return
	}
	defer _r.Close()

	resizer, err := image.Resizer(_r, image.ParseQuery(r.URL.Query()))
	if err != nil {
		log.Fatalf("image.Resizer: %v", err)
	}
	w.Header().Set("Cache-Control", "public, max-age=60")
	resizer(w)
}

func notFound(w http.ResponseWriter, path string) {
	w.WriteHeader(http.StatusNotFound)
	fmt.Fprintf(w, "file '%s' not found.", path)
}
```

### image.go

```go
package image

import (
	"errors"
	"golang.org/x/image/draw"
	"image"
	"image/gif"
	"image/jpeg"
	"image/png"
	"io"
	"log"
)

func Resizer(r io.Reader, q Query) (func(w io.Writer) error, error) {
	img, t, err := image.Decode(r)
	if err != nil {
		return nil, err
	}

	bounds := img.Bounds()
	log.Printf("'source':  width: %d, height, %d.", bounds.Dx(), bounds.Dy())

	builder := RectBuilder{q, bounds}
	srcRect := builder.SrcRect()
	dstRect := builder.DstRect()

	dst := image.NewRGBA(dstRect)
	draw.BiLinear.Scale(dst, dstRect, img, srcRect, draw.Over, nil)

	switch t {
	case "jpeg":
		return func(w io.Writer) error {
			return jpeg.Encode(w, dst, &jpeg.Options{Quality: 100})
		}, nil
	case "gif":
		return func(w io.Writer) error {
			return gif.Encode(w, dst, nil)
		}, nil
	case "png":
		return func(w io.Writer) error {
			return png.Encode(w, dst)
		}, nil
	default:
		return nil, errors.New("format error")
	}
}
```

### query.go

```go
package image

import (
	"net/url"
	str "strconv"
)

type Query struct {
	width, height int
	crop          Crop
}

type Crop int

const (
	Undefined Crop = iota
	Cover
)

func ParseQuery(v url.Values) Query {
	w := toSafetyInt(v.Get("w"))
	h := toSafetyInt(v.Get("h"))
	c := toCrop(v.Get("c"))
	return Query{w, h, c}
}

func toSafetyInt(s string) int {
	i, err := str.Atoi(s)
	if err != nil {
		return 0
	}
	return i
}

func toCrop(s string) Crop {
	switch s {
	case "cover":
		return Cover
	default:
		return Undefined
	}
}
```

### rect.go

```go
package image

import (
	"image"
)

type RectBuilder struct {
	q   Query
	src image.Rectangle
}

func (b RectBuilder) SrcRect() image.Rectangle {
	dx := float64(b.src.Dx())
	dy := float64(b.src.Dy())
	width := float64(b.q.width)
	height := float64(b.q.height)
	return srcRect(b.q.crop, dx, dy, width, height)
}

func (b RectBuilder) DstRect() image.Rectangle {
	dx := float64(b.src.Dx())
	dy := float64(b.src.Dy())
	width := float64(b.q.width)
	height := float64(b.q.height)
	return dstRect(dx, dy, width, height)
}

func srcRect(crop Crop, dx, dy, width, height float64) image.Rectangle {
	switch crop {
	case Undefined:
		return image.Rect(0, 0, int(dx), int(dy))
	case Cover:
		if width != 0 && height != 0 {
			srcRatio := dy / dx
			dstRatio := height / width
			if dstRatio > srcRatio {
				_dx := dy / dstRatio
				return image.Rect(int(dx/2-_dx/2), 0, int(dx/2+_dx/2), int(dy))
			} else {
				_dy := dx * dstRatio
				return image.Rect(0, int(dy/2-_dy/2), int(dx), int(dy/2+_dy/2))
			}

		} else if width == 0 && height != 0 {
			_dx := dx * height / dy
			return image.Rect(int(dx/2-_dx/2), 0, int(dx/2+_dx/2), int(dy))

		} else if width != 0 && height == 0 {
			_dy := dy * width / dx
			return image.Rect(0, int(dy/2-_dy/2), int(dx), int(dy/2+_dy/2))

		} else {
			return image.Rect(0, 0, int(dx), int(dy))
		}
	default:
		return image.Rect(0, 0, int(dx), int(dy))
	}
}

func dstRect(dx, dy, width, height float64) image.Rectangle {
	if width != 0 && height != 0 {
		return image.Rect(0, 0, int(width), int(height))

	} else if width == 0 && height != 0 {
		ratio := height / dy
		return image.Rect(0, 0, int(dx*ratio), int(height))

	} else if width != 0 && height == 0 {
		ratio := width / dx
		return image.Rect(0, 0, int(width), int(dy*ratio))

	} else {
		return image.Rect(0, 0, int(dx), int(dy))
	}
}
```

### storage.go

```go
package storage

import (
	"cloud.google.com/go/storage"
	"context"
)

const IMAGE_STORAGE_NAME = "storage"

var client *storage.Client

// err is pre-declared to avoid shadowing client.
func Init() (err error) {
	// client is initialized with context.Background() because it should
	// persist between function invocations.
	client, err = storage.NewClient(context.Background())
	return
}

func Reader(context context.Context, path string) (*storage.Reader, error) {
	bkt := client.Bucket(IMAGE_STORAGE_NAME)
	obj := bkt.Object(path)
	return obj.NewReader(context)
}
```
