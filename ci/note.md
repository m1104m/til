## CIでの覚書

- 2.0になってからはmechineはほぼ使わない
- dockerがメイン
- job間のファイルの受け渡しは persist_to_workspace, attach_workspace
- リポジトリの取得はcheckoutで取得（ここハマった）
- workflowにするとCの動作が別れる


フィールド | 内容
-- | --
version: | CircleCIのバージョンを指定
jobs: | ジョブ名
build: | ジョブ指定
docker: | ビルドコンテナ
docker: -image: | コンテナのイメージ
steps: | jobで実行する処理を定義
steps: - checkout | workディレクトリに、ソースをチェックアウト
steps: - run: | コマンド実行

参考：http://blog.livedoor.jp/harukisan7/archives/16625484.html


```
version: 2
jobs:
  build:
    working_directory: ~/repo
    docker:
      - image: circleci/node:8.11.3
    steps:
      - run: sudo npm install -g clean-css-cli
      - checkout
      - run: cleancss -o test_min.css test.css
      - persist_to_workspace:
           root: ~/repo
           paths:
             - test_min.css
  deploy:
    working_directory: ~/repo
    environment:
      - AWS_S3_BUCKET_NAME: sanwa-dl
    docker:
      - image: innovatorjapan/awscli:latest
    steps:
      - attach_workspace:
         at: ~/repo
      - run: pwd
      - run: ls -al
      - run: cd .. | ls -al
      - run:
          name: Deploy to S3
          command: |
            if [ "${CIRCLE_BRANCH}" == "master" ]; then
              aws s3 cp ./test_min.css s3://${AWS_S3_BUCKET_NAME}/pr/
            fi
workflows:
  version: 2
  build-and-deploy:
    jobs:
      - build
      - deploy:
          requires:
            - build
```
