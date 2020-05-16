# First step

이번 섹션에서는 Nest의 **기본핵심** 을 배울것입니다. Nest 응용프로그램의 필수요소에 익숙해지기 위해, 입문서적 차원에서 많은 부분의 기본의 CRUD를 제공하는 응용프로그램을 구축합니다.

## 언어

우리는 **`Typescript`** 를 좋아하지만, 무엇보다도 **`Node.js`** 좋아합니다. 그렇기 때문에 Nest는 **순수 자바스크립트**, 타입스크립트와 호환됩니다. Nest는 최신 언어 기능을 활용하므로 바닐라 JavaScript와 함께 사용하려면 **`Babel`** 컴파일러가 필요합니다.

우리는 대부분 우리가 제공하는 예제에서 TypeScript를 사용하지만 항상 코드 스니펫을 바닐라 JavaScript 구문으로 전환 할 수 있습니다.

## 전제조건

여러분의 운영체제에 **`Nede.js`**(>=8.9.0)이 반드시 있어야 합니다.

## 설치

Nest CLI를 사용하여 새 프로젝트를 설정하는 것은 매우 간단합니다. npm을 설치하면 OS 터미널에서 다음 명령을 사용하여 새 Nest 프로젝트를 만들 수 있습니다.

```bash
$ npm i -g @nestjs/cli
$ nest new project-name
```

프로젝트 디렉터리가 생성되고, 노드 모듈과 몇 개의 다른 상용구 파일이 설치되며, `**src/**` 디렉터리가 생성되어 여러 코어 파일로 채워집니다.

```
src
  ├──app.controller.ts
  ├──app.module.ts
  └──main.ts
```

**`app.controller.ts`** 단일 경로(라우트)의 기본 컨트롤러 샘플

**`app.module.ts`** 어플리케이션의 최상위 모듈

**`main.ts`** 핵심 함수 **`NestFactory`** 를 사용하여 Nest 애플리케이션 인스턴스를 작성하는 애플리케이션의 엔트리(entry) 파일입니다.

main.ts에는 비동기 함수가 포함되어있어 응용 프로그램을 부트스트랩합니다.



```typescript
// main.ts

import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(3000);
}
bootstrap();
```

Nest 어플리케이션 인스턴스를 생성하기 위해, 핵심 클래스 **`NestFactory`** 를 사용합니다. **`NestFactory`**는 어플리케이션 인스턴스를 생성하기 위해 허용하기 위해 몇가지 정적 메서드를 제공합니다. **`create()`** 메서드는  **`INestApplication`** 인터페이스를 수행하는 애플리케이션 객체를 리턴합니다. 이 객체는 다음 장에서 설명하는 일련의 메소드를 제공합니다. 위의 **`main.ts`** 예제에서 HTTP 리스너를 시작하면 응용 프로그램이 인바운드 HTTP 요청을 기다릴 수 있습니다.

Nest CLI로 스캐 폴딩 된 프로젝트는 개발자가 각 모듈을 전용 디렉터리에 보관하는 규칙을 따르도록 권장하는 초기 프로젝트 구조를 만듭니다.

## 플랫폼

Nest의 목표는 플랫폼의 영향을 받지않는 프레임워크입니다. 플랫폼 독립성을 통해 개발자는 여러 유형의 응용프로그램에서 활용할 수 있는 재사용 가능하도록 합니다. 기술적으로 Nest는 어댑터가 생성되면 모든 Node HTTP 프레임워크와 동작할 수 있습니다. 기본적으로 지원되는 두 가지 HTTP 플랫폼 인 **`express`**, **`fastify`** 있습니다. 필요에 가장 적합한 것을 선택할 수 있습니다.

```
platform-express: express잘 알다시피 node에서 제공하는 경량화된 웹 프레임워크입니다. 커뮤니티에서 구현한 많은 리소스를 갖추고 테스트 된 프로덕션용 라이브러리입니다. @nestjs/platform-express 패키지는 기본적으로 사용됩니다. 많은 사용자들은 익스프레스를 잘 이용하고 있으며, 그것을 가능하게 하기 위해 어떤 조치도 취할 필요가 없습니다.

platform-fastify: Fastify는 최고의 효율과 속도를 제공하는 데 매우 초점을 맞춘 고성능과 낮은 오버헤드 프레임워크입니다. 여기에서 사용법을 읽어 보십시오.
```

어떤 플랫폼을 사용하든, 자체 애플리케이션 인터페이스를 노출합니다. 이것들은 각각 **`NestExpressApplication`**, **`NestFastifyApplication`** 확인할 수 있습니다.

아래 예와 같이 유형을 NestFactory.create() 메소드로 전달하면 앱 개체는 특정 플랫폼에만 사용할 수 있는 메소드를 갖게 됩니다. 그러나 기본 플랫폼 API에 실제로 액세스하려는 경우가 아니라면 유형을 지정할 필요가 없습니다.

```typescript
const app = await NestFactory.create<NestExpressApplication>(AppModule);
```

## 어플리케이션 실행

설치 프로세스가 완료되면 OS 명령 프롬프트에서 다음 명령을 실행하여 HTTP 요청을 수신하는 응용 프로그램을 시작합니다.

```bash
$ npm run start
```

이 명령은 src/main.ts 파일에 정의한 포트에서 HTTP 서버를 수신하여 앱을 시작합니다. 응용 프로그램이 실행되고 나면 브라우저를 열고 http://localhost:3000/로 접속하면, Hello World! 메시지를 확인할 수 있습니다.
