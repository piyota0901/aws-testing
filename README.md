# aws-testing

## SAM Local
- [samlocal](https://github.com/localstack/aws-sam-cli-local)

- install
  ```powershell
  pip install aws-sam-cli-local
  ```
  - python3.8 だとエラー
  ```powershell
  ERROR: After October 2020 you may experience errors when installing or updating packages. This is because pip will change the way that it resolves dependency conflicts.

  We recommend you use --use-feature=2020-resolver to test your packages with the new resolver before it becomes the default.

  flask 1.1.4 requires Jinja2<3.0,>=2.10.1, but you'll have jinja2 3.1.1 which is incompatible.
  aws-sam-cli 1.46.0 requires typing-extensions==3.10.0.0, but you'll have typing-extensions 4.1.1 which is incompatible.
  ```
  - python3.9 で問題ない
## SAM Machine Learning
1. sam init
```powershell
❯ sam init

You can preselect a particular runtime or package type when using the `sam init` experience.
Call `sam init --help` to learn more.

Which template source would you like to use?
        1 - AWS Quick Start Templates
        2 - Custom Template Location
Choice: 1

Choose an AWS Quick Start application template
        1 - Hello World Example
        2 - Multi-step workflow
        3 - Serverless API
        4 - Scheduled task
        5 - Standalone function
        6 - Data processing
        7 - Infrastructure event management
        8 - Machine Learning
Template: 8

Which runtime would you like to use?
        1 - python3.9
        2 - python3.8
Runtime: 2

Based on your selections, the only Package type available is Image.
We will proceed to selecting the Package type as Image.

Based on your selections, the only dependency manager available is pip.
We will proceed copying the template using pip.

Select your starter template
        1 - PyTorch Machine Learning Inference API
        2 - Scikit-learn Machine Learning Inference API
        3 - Tensorflow Machine Learning Inference API
        4 - XGBoost Machine Learning Inference API
Template: 2

Project name [sam-app]: testing_lambda_on_ml

Cloning from https://github.com/aws/aws-sam-cli-app-templates (process may take a moment)

    -----------------------
    Generating application:
    -----------------------
    Name: testing_lambda_on_ml
    Base Image: amazon/python3.8-base
    Architectures: x86_64
    Dependency Manager: pip
    Output Directory: .
    Next steps can be found in the README file at ./testing_lambda_on_ml/README.md


    Commands you can use next
    =========================
    [*] Create pipeline: cd testing_lambda_on_ml && sam pipeline init --bootstrap
    [*] Test Function in the Cloud: sam sync --stack-name {stack-name} --watch
```

1. requirements.txtのインストール
```powershell
> cd testing_lambda_on_ml/app
> pip install -r .\requirements.txt
Collecting scikit-learn==0.23.2
  Downloading scikit_learn-0.23.2-cp38-cp38-win_amd64.whl (6.8 MB)
     |████████████████████████████████| 6.8 MB 34 kB/s
Collecting pillow==9.0.1
  Using cached Pillow-9.0.1-cp38-cp38-win_amd64.whl (3.2 MB)
Collecting numpy>=1.13.3
  Using cached numpy-1.22.3-cp38-cp38-win_amd64.whl (14.7 MB)
Collecting joblib>=0.11
  Using cached joblib-1.1.0-py2.py3-none-any.whl (306 kB)
Collecting threadpoolctl>=2.0.0
  Using cached threadpoolctl-3.1.0-py3-none-any.whl (14 kB)
Collecting scipy>=0.19.1
  Using cached scipy-1.8.0-cp38-cp38-win_amd64.whl (36.9 MB)
Installing collected packages: numpy, joblib, threadpoolctl, scipy, scikit-learn, pillow
Successfully installed joblib-1.1.0 numpy-1.22.3 pillow-9.0.1 scikit-learn-0.23.2 scipy-1.8.0 threadpoolctl-3.1.0
```

1. ビルド
```powershell
> cd ../
> sam build
Your template contains a resource with logical ID "ServerlessRestApi", which is a reserved logical ID in AWS SAM. It could result in unexpected behaviors and is not recommended.
Building codeuri: XXXXXX\testing_lambda_on_ml runtime: None metadata: {'Dockerfile': 'Dockerfile', 'DockerContext': 'XXXXXX\\sam_ml\\testing_lambda_on_ml\\app', 'DockerTag': 'python3.8-v1'} architecture: x86_64 functions: ['InferenceFunction']
Building image for InferenceFunction function
Setting DockerBuildArgs: {} for InferenceFunction function
Step 1/5 : FROM public.ecr.aws/lambda/python:3.8
3.8: Pulling from lambda/python
e6842361273f: Pull complete
2c57c8f988d9: Pull complete
a71e66825c17: Pull complete
1418415b0a5d: Pull complete
d0d18376340c: Pull complete
cf255487963a: Pull complete
Status: Downloaded newer image for public.ecr.aws/lambda/python:3.8 ---> 304388baf5b9
Step 2/5 : COPY app.py requirements.txt ./
 ---> c15245b9711c
Step 3/5 : COPY model /opt/ml/model
 ---> 58ca25518459
Step 4/5 : RUN python3.8 -m pip install -r requirements.txt -t .
 ---> Running in 7307bae5c107
Collecting scikit-learn==0.23.2
  Downloading scikit_learn-0.23.2-cp38-cp38-manylinux1_x86_64.whl (6.8 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 6.8/6.8 MB 9.7 MB/s eta 0:00:00
Collecting pillow==9.0.1
  Downloading Pillow-9.0.1-cp38-cp38-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (4.3 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 4.3/4.3 MB 9.1 MB/s eta 0:00:00
Collecting joblib>=0.11
  Downloading joblib-1.1.0-py2.py3-none-any.whl (306 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 307.0/307.0 KB 3.9 MB/s eta 0:00:00
Collecting numpy>=1.13.3
  Downloading numpy-1.22.3-cp38-cp38-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (16.8 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 16.8/16.8 MB 1.5 MB/s eta 0:00:00
Collecting scipy>=0.19.1
  Downloading scipy-1.8.0-cp38-cp38-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (41.6 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 41.6/41.6 MB 7.8 MB/s eta 0:00:00
Collecting threadpoolctl>=2.0.0
  Downloading threadpoolctl-3.1.0-py3-none-any.whl (14 kB)
Installing collected packages: threadpoolctl, pillow, numpy, joblib, scipy, scikit-learn
Successfully installed joblib-1.1.0 numpy-1.22.3 pillow-9.0.1 scikit-learn-0.23.2 scipy-1.8.0 threadpoolctl-3.1.0
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv
 ---> de43128f678d
Step 5/5 : CMD ["app.lambda_handler"]
 ---> Running in 63c86bb478bf
 ---> 47906a5609b9
Successfully built 47906a5609b9
Successfully tagged inferencefunction:python3.8-v1


Build Succeeded

Built Artifacts  : .aws-sam\build
Built Template   : .aws-sam\build\template.yaml

Commands you can use next
=========================
[*] Invoke Function: sam local invoke
[*] Test Function in the Cloud: sam sync --stack-name {stack-name} --watch
[*] Deploy: sam deploy --guided
```

1. invoke
```powershell
> sam local invoke InferenceFunction --event events/event.json
Invoking Container created from inferencefunction:python3.8-v1
Building image.................
Skip pulling image and use local one: inferencefunction:rapid-1.46.0-x86_64.

START RequestId: 84bdc547-2071-40e9-9a84-56400125f305 Version: $LATEST
END RequestId: 84bdc547-2071-40e9-9a84-56400125f305
REPORT RequestId: 84bdc547-2071-40e9-9a84-56400125f305  Init Duration: 0.36 ms  Duration: 988.72 ms     Billed Duration: 989 ms Memory Size: 5000 MB    Max Memory Used: 5000 MB
{"statusCode": 200, "body": "{\"predicted_label\": 3}"}
```


### 参考
- [AWS SAM が AWS Lambda 向けの機械学習 (ML) 推論テンプレートを提供](https://aws.amazon.com/jp/about-aws/whats-new/2021/06/aws-sam-launches-machine-learning-inference-templates-for-aws-lambda/)
- [Deploying machine learning models with serverless templates](https://aws.amazon.com/jp/blogs/compute/)