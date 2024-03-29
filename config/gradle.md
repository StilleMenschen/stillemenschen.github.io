# gradle

环境变量`GRADLE_USER_HOME`指定主目录，默认在`${USER_HOME}/.gradle`

- `${GRADLE_USER_HOME}/init.gradle`
- `${GRADLE_USER_HOME}/init.d/build.gradle`

```groovy
allprojects {
  repositories {
    def ALIYUN_REPOSITORY_URL = 'https://maven.aliyun.com/repository/public/'
    def ALIYUN_JCENTER_URL = 'https://maven.aliyun.com/repository/jcenter/'
    def ALIYUN_GOOGLE_URL = 'https://maven.aliyun.com/repository/google/'
    def ALIYUN_GRADLE_PLUGIN_URL = 'https://maven.aliyun.com/repository/gradle-plugin/'
    all {
      ArtifactRepository repo ->
        if (repo instanceof MavenArtifactRepository) {
          def url = repo.url.toString()
          if (url.startsWith('https://repo1.maven.org/maven2/')) {
            project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_REPOSITORY_URL."
            remove repo
          }
          if (url.startsWith('https://jcenter.bintray.com/')) {
            project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_JCENTER_URL."
            remove repo
          }
          if (url.startsWith('https://dl.google.com/dl/android/maven2/')) {
            project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_GOOGLE_URL."
            remove repo
          }
          if (url.startsWith('https://plugins.gradle.org/m2/')) {
            project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_GRADLE_PLUGIN_URL."
            remove repo
          }
        }
    }
    maven {
      url ALIYUN_REPOSITORY_URL
    }
    maven {
      url ALIYUN_JCENTER_URL
    }
    maven {
      url ALIYUN_GOOGLE_URL
    }
    maven {
      url ALIYUN_GRADLE_PLUGIN_URL
    }
  }

  buildscript {
    repositories {
      maven {
        url 'https://maven.aliyun.com/repository/public/'
      }
      maven {
        url 'https://maven.aliyun.com/repository/google/'
      }

      all {
        ArtifactRepository repo ->
          if (repo instanceof MavenArtifactRepository) {
            def url = repo.url.toString()
            if (url.startsWith('https://repo1.maven.org/maven2') || url.startsWith('https://jcenter.bintray.com/') ||
              url.startsWith('https://dl.google.com/dl/android/maven2/')) {
              project.logger.lifecycle "Repository ${repo.url} replaced by $REPOSITORY_URL."
              remove repo
            }
          }
      }
    }
  }
}
```

Last Modified 2022-07-21