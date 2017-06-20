# gitlab-ci-android
This Docker image contains the Android SDK and most common packages necessary for building Android apps in a CI tool like GitLab CI. Make sure your CI environment's caching works as expected, this greatly improves the build time, especially if you use multiple build jobs.

A `.gitlab-ci.yml` with caching of your project's dependencies would look like this:

```
image: dyangalih/gitlab-ci-android:latest

stages:
- check
- build
- deploy

before_script:
  - export GRADLE_USER_HOME=/cache/.gradle

cache:
  key: ${CI_PROJECT_ID}
  paths:
  - .gradle/

#job untuk checking
check:
  stage: check
  only:
        - develop
  script:
      - ./gradlew assembleDebug

#job untuk build
build:
  stage: build
  only:
      - develop
  script:
    - ./gradlew --stacktrace clean assembleRelease crashlyticsUploadDistributionQaRelease
  artifacts:
    paths:
    - app/build/outputs/apk

#job untuk deploy
deploy:
  stage: deploy
  only:
      - master
  script:
    - ./gradlew --stacktrace clean publishApkPlayRelease
  artifacts:
    paths:
    - app/build/outputs/apk
```
