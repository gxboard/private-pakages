# private-pakages (Satis)

Satis Run example 

소스를 내려받아 실행할 수 도 있고, 도커 이미지로 실행할 수도 있다.

## Run from source

- `composer create-project composer/satis` 명령으로 Satis 설치 
- `php satis/bin/satis ./satis.json <output-dir>` 리파지터리를 빌드/생성 한다.


### Satis 프로젝트 생성 

```
php composer.phar create-project composer/satis --stability=dev --keep-vcs
```

### Setup 


아래와 같은 `satis.json` 구성 파일을 작성하고, 쓰기가능한 아웃풋 디렉터리를 생성한다.  


```json
{
  "name": "My Repository",
  "homepage": "http://private-packages.example.org",
  "repositories": [
    { "type": "vcs", "url": "https://github.com/gxboard/symfony" }
  ],
  "require-all": true,
  "output-dir": "public",
  "archive": {
      "directory": "dist",
      "prefix-url": "https://private-packages.cdn.example.org",
      "skip-dev": true
    }
}
```

 - fork 한 symfony 프로젝트를 추가하였다.


### Build 

패키지 저장소를 빌드/생성

```
php satis/bin/satis build 
php satis/bin/satis build satis.json public/
```



## Run as Docker Container

Windows7 용으로 Docker Toolbox 설치
https://docs.docker.com/toolbox/toolbox_install_windows/ 

Docker QuickStart Terminal 을 실행하고, composer/satis 이미지를 가져와서 빌드명령을 실행한다.

```bash 
docker pull composer/satis
docker run --rm -it -v /c/Users/Administrator/IdeaProjects/private-pakages/build:/build composer/satis
docker run --rm -it -v /c/Users/Administrator/IdeaProjects/private-pakages/build:/build composer/satis
```

 - rm 종료되면 자동으로 컨테이너를 삭제한다.
 - it 
   - --interactive Keep STDIN open even if not attached 
   - --tty Allocate a pseudo-TTY (슈도 TTY 할당)
 - v --volume Bind mount a volume
   - 절대 경로 또는 볼륨이름

`/c/Users/Administrator/IdeaProjects/private-pakages/output` 에 저장된다. `Dockerfile` 에서 `/build/satis.json` `/build/output` 에 저장되도록 구성되어 있다.


명시적으로 구성 파일과 아웃풋 디렉터리를 지정하거나 
엔트리 포인트를 `/bin/sh` 로 변경한다.

```bash
docker run --rm -it -v /c/Users/Administrator/IdeaProjects/private-pakages:/build composer/satis /satis/bin/satis build /build/satis.json /build/public

# 엔트리포인트 지정
docker run --rm -it --entrypoint /bin/sh -v /c/Users/Administrator/IdeaProjects/private-pakages:/build composer/satis
```

엔트리포인트를 쉘로 변경해서 들어가보면, 볼륨이 마운트 된 것을 확인할 수 있다.

```log
/satis # ls -l /build/
total 1845
-rwxrwxrwx    1 1000     50            2961 Aug 25 07:34 README.md
-rwxrwxrwx    1 1000     50         1882683 Aug 25 02:39 composer.phar
drwxrwxrwx    1 1000     50               0 Aug 25 06:52 public
-rwxrwxrwx    1 1000     50             364 Aug 25 07:19 satis.json
```


Run the image (with Composer cache from host):

```bash
docker run --rm -it -v /build:/build -v $COMPOSER_HOME:/composer composer/satis
```

 - 로컬에 캐시된 컴포저를 사용할 수 있다. 



## 참고 문서

- https://getcomposer.org/doc/articles/handling-private-packages-with-satis.md
- Satis https://github.com/composer/satis
- http://blog.servergrove.com/2015/04/29/satis-building-composer-repository/


