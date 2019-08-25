# circleci

1 nodeだけだったら無料。

本番環境と開発環境の情報をlocalで合わせて管理はしたくなかったので、

本番 => circleci
開発 => local

でbuild deployするようにしよう。

## firebase連携

ref: https://circleci.com/docs/2.0/deployment-integrations/#firebase

## gcloud連携

deployの中でgcloudコマンドを利用している（functionのdeploy）
nodejsのimageをbase imageとして利用しているので install とかしないといかんのかなと思っていたが、
orbsを利用するとできるかな。

### orbsの荒っぽい理解

いくつかコンセプトはあるが、ざっくり言うと「containerをベースにして自作のjobとstepが作れる（引数とかも設定できる）」っていう感じ。

今回使ったのは https://circleci.com/orbs/registry/orb/circleci/gcp-cli?version=1.8.2 これ。

install-and-initailze-cliみたいなjobもあるけどこれやるとjob間で引き継ぎがないので、
せっかくインストールした `gcloud` コマンドが使えないので `gcp-cli/install` と `gcp-cli/initialize` stepを使うようにした。
