# 프로젝트 문의 / 제안
프로젝트와 관련한 내용은 <https://django.seolpypo.com/>에 이야기해주시면 감사합니다.

또한 패키지의 상세 설명을 설표의장고 사이트에서 확인하실 수 있습니다.

# 프로젝트 목적
백업파일을 이용하여 티스토리 블로그에 작성한 글들을 Django로 이전하여 운영할 수 있도록 합니다.

티스토리와 동일한 url 구조를 사용합니다.

## 사용방법
### 패키지 설치
디스토리 패키지를 설치합니다.

tqdm, requests, bs4(BeautifulSoup), selenium, django, django-summenote를 필요로 합니다.
```
>> pip install seolpyo_dstory
```

### 장고 프로젝트 생성
mysite라는 폴더를 생성하고, 폴더 안에 장고 프로젝트를 생성합니다.
```
>> mkdir mysite
>> cd mysite
>> django-admin startproject config .
```

### 기초 설정
mysite > config > settings.py를 찾아 이름을 settings_base.py로 변경합니다.

settings.py를 새로 생성하고, 다음과 같이 작성합니다.

seolpyo_dsotry.settings 파일은 장고의 기초설정을 빠르게 하기 위해 작성된 파일입니다.
```
# config/settings.py
from .settings_base import *
from seolpyo_dstory.settings import *
for i in apps:
  if i not in INSTALLED_APPS: INSTALLED_APPS.append(i)

TEMPLATES['OPTIONS']['context_processors'].append(processor)
MEDIA_URL = 'media/'
MEDIA_ROOT = BASE_DIR / 'media'
```


별도의 이용자 모델이 있는 경우, AUTH_USER_MODEL 선언해주어야 합니다. 현재 설정에서 기본값은 다음과 같습니다.
```
# config/settings.py
AUTH_USER_MODEL = 'seolpyo_dstory.User'
```

urls.py를 열어 내용을 다음과 같이 변경합니다.
```
# config/urls.py
from django.conf import settings
from django.contrib import admin
from django.urls import path, include


urlpatterns = [
    path('admin/', admin.site.urls),
    path('summernote/', include('django_summernote.urls')),
    path('', include('seolpyo_dstory.urls')),
]

if not settings.DEBUG:
    handler400 = 'seolpyo_dstory.views.handler'
    handler403 = 'seolpyo_dstory.views.handler'
    handler404 = 'seolpyo_dstory.views.handler'
    handler500 = 'seolpyo_dstory.views.handler'

if settings.DEBUG:
    from django.conf.urls.static import static
    urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
    urlpatterns += static('media/', document_root=(settings.BASE_DIR / 'media/'))
```

### 마이그레이션
관리자를 추가하기 위해 마이그레이션을 생성하고, 적용합니다.
```
>> python manage.py makemigrations
>> python manage.py migrate
```

#### 관리자 추가하기
createsuperuser 명령을 통해 관리자를 추가합니다.
```
>> python manage.py createsuperuser
```

### 티스토리 백업파일을 이용해 글 복구하기
이 패키지는 티스토리 블로그 이사를 위한 2가지 방법을 제공합니다.

하나는 백업파일만을 이용한 단순 복구, 다른 하나는 백업파일과 티스토리 블로그에 접속해 공개설정, 비밀번호, 문자 주소 등의 정보를 가져와 적용하는 것입니다.

티스토리 백업파일은 글의 공개 여부나 보호글의 비밀번호, 임의로 설정한 문자 주소 정보를 제공하지 않습니다.

다운로드한 티스토리 백업파일을 mysite 폴더에 이동시킨 다음, tistory.zip이라는 이름으로 변경합니다.

#### 백업파일을 통한 단순복구
장고 shell을 실행하고, 다음 명령어를 입력합니다.
```
>> python manage.py shell
>> from seolpyo_dstory.utils import resotre as r
>> r()
```

#### 백업파일과 티스토리 블로그를 통한 복구
장고 shell을 실행하고, 다음 명령어를 입력합니다.

복구 과정에서 티스토리에 로그인하기 위한 로그인 아이디와 로그인 비밀번호를 요구합니다.

2단계 인증을 사용 중인 경우 로그인에 실패하기 때문에 2단계 인증을 사용하지 않는 계정이어야 합니다.
```
>> python manage.py shell
>> from seolpyo_dstory.utils import resotre2 as r
>> r()
```

### 확인하기
장고 프로젝트를 실행하고, 복구된 글을 확인해봅니다.

로컬 서버에서 실행하는 경우 기본 주소는 127.0.0.1:8000입니다.
```
>> python manage.py runserver
```

# 그 외 설정들
여러 설정들을 만들고 적용할 수 있도록 했습니다.

설정들에 대한 설명은 [설표의장고](https://django.seolpyo.com/)에서 확인하실 수 있습니다.


# 샘플 이미지
다음은 해당 패키지를 통해 티스토리 블로그를 이사하여 운영 중인 사이트의 샘플 이미지입니다.

샘플 사이트 : <https://dstory.seolpyo.com/>

티스토리 블로그 : <https://white-seolpyo.tistory.com/>

![이미지 1](https://github.com/white-seolpyo/seolpyo_dstory/blob/main/img1.png?raw=true)

![이미지 2](https://github.com/white-seolpyo/seolpyo_dstory/blob/main/img2.png?raw=true)

