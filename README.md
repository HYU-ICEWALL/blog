# 아이스월 기술 블로그

[hyu-icewall.github.io/blog](https://hyu-icewall.github.io/blog)

이런저런 것들을 테스트하고 아이스월에서 일어나는 기술적인 일들에 대해 기록하려고 만든 블로그입니다. 사용 방법은 간단합니다. git 사용법을 조금이라도 알고 있으면 바로 블로그를 사용할 수 있습니다.

```shell
$ git clone https://github.com/HYU-ICEWALL/blog
$ git pull
(이런 저런 작업 작업)
$ git add 수정한-파일
$ git commit -m "뭐 수정함"
$ git push
```

git을 도저히 못 쓰겠다면, GitHub에서 제공하는 웹 에디터를 사용하는 것도 한 방법입니다.

git clone 후, rake를 이용하여 포스트를 쉽게 작성할 수 있습니다.

```bash
$ rake post title="A Title" [date="2015-08-16"][tags=[tag1,tag2]]
$ rake draft title="A Title" [date="2015-08-16"][tags=[tag1,tag2]]
$ rake preview
```

수동으로 포스팅 을 하는 방법은 다음과 같습니다.

1. `_post` 폴더 아래 yyyy-mm-dd-적절한-이름.md 형태의 파일을 생성한다.

2. 생성한 파일을 열어 편집을 시작한다. 이 때, yaml로 된 헤더를 추가해야한다.
   yaml로 된 헤더는 다음과 같습니다. 잘 모르겠다면 `_post` 폴더의 `2016-09-19-hello-world.md` 를 참고하시길 바랍니다.

   ```yaml
   ---
   layout: post
   title: "블로그 제목"
   descripttion: "포스팅 설명, 대충 적어도 됨"
   data: "yyyy-mm-dd 형태의 날짜 (e.g. 2016-09-30)"
   tags: [적당한, 태그들]
   share: true
   ---
   ```

3. 편집이 끝나면 저장을 하고, 깃을 이용하여 푸쉬를 합니다.

여러 사람들이 동시에 포스팅하게 되면서 충돌이 나는 등, 여러가지 복잡한 문제들이 존재하는거 같지만 그건 그 때 가서 생각해봅시다. 추가적으로 이 블로그는 Github-pages를 이용하여 만들어졌으며, Jekyll을 이용하여 페이지를 생성합니다.

자세한 작동 원리를 알고 싶다면, 한국어로 번역된 [Jekyll 문서](https://jekyllrb-ko.github.io/)를 보거나 [Nolboo](https://nolboo.kim/)님의 [지킬로 깃허브에 무료 블로그 만들기](https://nolboo.kim/blog/2013/10/15/free-blog-with-github-jekyll/)를 참고하시길 바랍니다. 또한, 블로그의 유지보수를 하기 위해서는 Liquid라는 템플릿팅 엔진에 대해서도 숙지해야합니다. 이를 위해 [공식 문서](https://shopify.github.io/liquid/)를 볼 것을 추천합니다. 감사합니다.
