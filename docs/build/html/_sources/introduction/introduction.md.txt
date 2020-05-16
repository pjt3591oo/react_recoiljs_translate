# Introduction

NestJS는 효율적이고 확장 가능한 **`NodeJS`** 서버사이드 구축을 위한 서버사이드 프레임워크입니다. 프로그레시브 자바스크립트 사용하며 **`타입스크립트`** 기반의 빌드 및 완벽지원(아직은 순수 자바스크립트 개발 개능), OOP(객체지향 프로그래밍), FP(함수형 프로그래밍), FRP(함수 반응형 프로그래밍) 요소를 결합합니다.

아래에서 Next는 **`Express`** 같은 HTTP 서버 프레임워크를 사용하고 선택적으로 **`Fastify`** 이용하여 구성할 수 있습니다. 

Nest는 Node.js의 Express/Fastify 프레임워크의 공용 라이브러리를 추상적으로 제공하지만, API를 개발자들에게 직접적으로 공개하기도 합니다. 이를 통해 개발자들은 플랫폼(NestJS)에서 사용가능한 서드파티 모듈을 자유롭게 사용할 수 있습니다.



## Philosophy(철학)

최근 몇 년간 Node.js 덕분에 자바스크립트는 프론트와 백엔드 모두에서 웹의 ""가 되었습니다. 이것은 개발 품질, 빠른 생산,  테스트, 확장성을 개선한 **`Angular`**, **`React`**, **`Vue`** 같은 프로젝트들이 증가했습니다. 그러나 Node를 위한 훌륭한 라이브러리, 헬퍼 및 도구가 있지만 이들 중 어느것도 아키텍처의 주요 문제를 효과적으로 해결하지 못합니다.

Nest는 팀과 개발자들에게 테스트, 확장성, 느슨한 결합, 쉬운 유지보수를 위한 응용프로그램을 만들 수 있도록 아키텍저를 제공합니다.



## Installation(설치)

시작하기 위해, **`Nest CLI`** 사용하여 스캐 폴드하거나 스타터 프로젝트를 복제합니다.

CLI를 이용하여프로젝트를 스캐폴딩하기 위해 다음과 같은 명령어를 실행합니다. 이것은 프로젝트 디렉터리를 생성하고 초기 핵심 Nest 파일 및 지원 모듈을 채워 프로젝트의 기본 구조를 생성합니다. **`Nest CLI`** 사용하여 프로젝트를 생성하는 것은 처음 사용하는 사용자에게 권장합니다. 첫 번째 단계에서는 이 방법을 계속 사용합니다.

```bash
$ npm i -g @nestjs/cli
$ nest new project-name
```

또는 **Git**과 함께 타입스크립트 프로젝트를 설치하려면 다음을 실행하세요

```bash
$ git clone https://github.com/nestjs/typescript-starter.git project
$ cd project
$ npm install
$ npm run start
```

브라우저를 열고 http://localhost:3000 접속

시작 프로젝트의 자바스크립트의 특징을 설치하기 위해, 위의 명령 순서에서 javascript-starter.git을 사용합니다.

여러분은 npm또는 yarn을 이용하여 제공할 파일 또는 코어를 명시하여 프로젝트를 수동으로 생성할 수 있습니다. 

```bash
$ npm i --save @nestjs/core @nestjs/common rxjs reflect-metadata
```

