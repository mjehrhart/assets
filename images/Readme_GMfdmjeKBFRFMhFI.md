# AWS Lambda & Rust Deployment Method

# Prerequisite
This guide is written for macOS users. Make sure you have these following items installed and configured if needed. Installing these items takes only a few minutes.

- AWS SAM CLI
	https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install-linux.html

- Zig - cross compilation  
  https://ziglang.org  
	```brew install zig```

- JQ - command-line JSON processor
	```brew install jq```

- Artillery - used for rapid testing  
  ```npm install -g artillery@latest```

# Setup
Open your mac terminal to your projects home directory
- ```sam init```
- ```mkdir build```
- Make any changes to Makefile
- ```cp Makefile build/```
- ```mkdir .cargo```
- ```touch .cargo/config.toml```
- Make any changes to template.yaml  

Once all this is done, go ahead and do your coding stuff.  As of the time of writing this, various rust crates do not work in aws lambda or at least I haven't been able to get them to work.  So be careful and take your time testing crates.  For me, it was a lot of trial and error.

### .cargo/config.toml  

Add the following to the file. This will allow for cross compilation so this can run on AWS custom runtime (amazon linux 2)

```bash
[target.x86_64-unknown-linux-musl]
linker = "rust-lld"
```

# Commands
- ```cargo build --release --target x86_64-unknown-linux-musl```
- ```cp target/x86_64-unknown-linux-musl/release/{APP_NAME} build/bootstrap```
- ```make build```
- ```make deploy```

# Testing
- ```make tests-unit``` (may fail)
- ```make tests-integ```

# Helpful Links
- https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/building-custom-runtimes.html

- https://github.com/aws-samples/serverless-rust-demo

- https://github.com/awslabs/aws-lambda-rust-runtime/blob/main/lambda-http/examples/hello-http.rs

# Note
I did not use ```sam build``` or ```sam deploy``` in this routine.  Honestly, I am not sure why both are not needed but they aren't if you follow this guide. There is an oddity about the Makefile and I don't understand why but this works if you put a Makefile in the project's root directory as well as placing a copy in the build/ directory.

# Screenshots  

#### Makefile
Here is what my Makefile looks like (I got it from https://github.com/aws-samples/serverless-rust-demo/blob/main/Makefile. Works great.)

<img width="50%" alt="images/Screen Shot 2022-05-10 at 10.04.55 AM_DXMSvanyjLN1Hood.png" src="https://raw.githubusercontent.com/mjehrhart/assets/main/images/Screen Shot 2022-05-10 at 10.04.55 AM_DXMSvanyjLN1Hood.png">

#### template.yaml
Note the CodeUri points to the build/ directory.  This is where the bootstrap file be put.  So the CodeUri points to the location of the bootstrap for that function.  

<img width="50%" alt="images/Screen Shot 2022-05-10 at 10.00.09 AM_NDCdNwvovL0Tjp0Y.png" src="https://raw.githubusercontent.com/mjehrhart/assets/main/images/Screen Shot 2022-05-10 at 10.00.09 AM_NDCdNwvovL0Tjp0Y.png">
