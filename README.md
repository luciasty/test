<p align="center">
  <a href="http://nestjs.com/" target="blank"><img src="https://nestjs.com/img/logo-small.svg" width="200" alt="Nest Logo" /></a>
</p>

[circleci-image]: https://img.shields.io/circleci/build/github/nestjs/nest/master?token=abc123def456
[circleci-url]: https://circleci.com/gh/nestjs/nest

  <p align="center">A progressive <a href="http://nodejs.org" target="_blank">Node.js</a> framework for building efficient and scalable server-side applications.</p>
    <p align="center">
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/v/@nestjs/core.svg" alt="NPM Version" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/l/@nestjs/core.svg" alt="Package License" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/dm/@nestjs/common.svg" alt="NPM Downloads" /></a>
<a href="https://circleci.com/gh/nestjs/nest" target="_blank"><img src="https://img.shields.io/circleci/build/github/nestjs/nest/master" alt="CircleCI" /></a>
<a href="https://coveralls.io/github/nestjs/nest?branch=master" target="_blank"><img src="https://coveralls.io/repos/github/nestjs/nest/badge.svg?branch=master#9" alt="Coverage" /></a>
<a href="https://discord.gg/G7Qnnhy" target="_blank"><img src="https://img.shields.io/badge/discord-online-brightgreen.svg" alt="Discord"/></a>
<a href="https://opencollective.com/nest#backer" target="_blank"><img src="https://opencollective.com/nest/backers/badge.svg" alt="Backers on Open Collective" /></a>
<a href="https://opencollective.com/nest#sponsor" target="_blank"><img src="https://opencollective.com/nest/sponsors/badge.svg" alt="Sponsors on Open Collective" /></a>
  <a href="https://paypal.me/kamilmysliwiec" target="_blank"><img src="https://img.shields.io/badge/Donate-PayPal-ff3f59.svg"/></a>
    <a href="https://opencollective.com/nest#sponsor"  target="_blank"><img src="https://img.shields.io/badge/Support%20us-Open%20Collective-41B883.svg" alt="Support us"></a>
  <a href="https://twitter.com/nestframework" target="_blank"><img src="https://img.shields.io/twitter/follow/nestframework.svg?style=social&label=Follow"></a>
</p>
  <!--[![Backers on Open Collective](https://opencollective.com/nest/backers/badge.svg)](https://opencollective.com/nest#backer)
  [![Sponsors on Open Collective](https://opencollective.com/nest/sponsors/badge.svg)](https://opencollective.com/nest#sponsor)-->

## 📌  트러블 슈팅

## 📌  트러블 슈팅

- 캠핑장 검색 시 데이터가 많아 로드뷰에 과도한 로딩 시도

- 캠핑장 검색 시 데이터가 많아 로드뷰에 과도한 로딩 시도
    - Elasticsearch 를 통함 검색 고도화
    
    ```jsx
    문제 : 기존 like로 검색을 하게 되면 쿼리 증가로 인한 사이트 속도 저하 및 정확한 검색 결과 산출이 어려운 문제 발생
    
    과정 : 
    	-> 서비스는 각각의 도메인 (캠핑장 / 레시피 / 상품) 별 mysql table 이 나눠져 있음 
    	-> 3번의 쿼리가 필요함
    	-> like 검색으로 전방일지가 아닌 단어 포함으로 검색 할 경우 index 를 사용하지 않아 레이턴시가 길어짐
    
    결과 : 역인덱싱을 제공하는 es 를 통해 통합 검색을 구현하여 속도가 빨라짐
    ```
    
    - 페이지네이션을 통한 범위 데이터 서빙으로 용량을 줄여 레이턴시를 줄임
- 캠핑장 검색결과에 로드뷰가 아닌 캠핑장 이미지 삽입시도
    
    ```jsx
    문제 : puppeteer 라이브러리로 요청 시도 하였으나 너무 대량의 요청이 발생하여 
    request limit over (이하 status 429) 발생
    
    과정 : 확인 결과 해당 api에서 분당 최대 200회 요청이 가능하며 200회 초과 시 status 429 발생
    
    결과 : 분당 200회 미만으로 요청하기 위해 크롤링 시 시간 설정을 하여 분당 120회 요청으로 변경
    ```
    
    ```jsx
    문제 : 카카오 지도 api를 이용해 캠핑장 정보를 받아오는 과정에서 이미지를 제공하지 않는 문제 발생
    
    과정 :
    			-> 이미지 크롤링을 시도하였으나 axios get 요청 시 xhr 통신이 막혀 ssl 인증 에러 발생
    			-> curl 라이브러리로 재시도 하였으나 동일한 에러 발생
    			-> 로컬 환경에서 테스트 하기 위해 로컬에서 작동 가능한 라이브러리를 검색
    			-> puppeteer 라이브러리로 요청 시 이미지 크롤링 가능
    
    결과 : 하지만 puppeteer 라이브러리로 시도 시 이미지 크롤링 속도가 과하게 느린 문제가 확인되어
    			추가 문제 해결 방법 고민 상태임
    ```
    
- Api 스케줄러로 주기적으로 실행시, 변함 없는 데이터는 그대로 유지 변화가 생긴 데이터는 delete 메소드로 db를 비우고 실행
    
    ```jsx
    문제 : api 호출시 insert 메소드로 인해서 기존에 데이터가 존재하더라도 중복 입력되는 문제 발생 
    
    과정 : 
    			-> delete 메소드로 데이터 삭제 후 insert를 진행하였으나 불필요한 삭제가 일어남
    			-> 변경이 없는 데이터는 삭제할 필요가 없다고 판단, On duplicate key update를 적용
    
    결과 : 이미 가져온 데이터 중 변경되지 않는 데이터는 더이상 요청하지 않도록 구현 계획 중
    ```
    
- 회원 탈퇴
    
    ```jsx
    문제 : 회원 탈퇴 버튼을 누르면 너무 쉽게 회원 탈퇴가 되어 당황을 했다는 사용자 피드백
    
    과정 :
    		-> 회원 탈퇴 시 프론트에서 bcrypt 처리 된 유저의 비밀번호를 불러와 현재 입력된 비밀번호를 암호화 시켜 
    			서로 일치하면 회원 탈퇴를 진행하도록 처리 시도
    		-> 프론트에서 위와 같은 처리를 하게 되면 암호화 로직이 공개적으로 보여지기에 백에서 처리하도록 시도
    
    결과 : 백에서 현재 입력된 비밀번호와 db에서 불러온 유저의 비밀번호를 대조하여 일치하면 회원 탈퇴를 진행
    ```
    
    ```jsx
    문제 : 회원 탈퇴 진행 시 조인 했을 때 실제 사용자가 없기 때문에 게시물 조회 실패
    
    과정 : 
    		-> 회원 탈퇴 시 탈퇴한 유저가 작성한 게시글, 댓글, 리뷰와 좋아요 까지 모두 delete 되도록 구현하였으나
    			사이트 목적인 소통, 즉 커뮤니티의 기능이 상실된다고 판단
    		-> 탈퇴한 사용자를 delete -> softdelete 처리
    		-> 게시물 조회 시, typeorm 특성 상 기본적으로 delete_at = null 조건을 추가함
    
    결과 : typeorm 기능 중 withDeleted = true 옵션을 사용하여 삭제된 유저정보도 join 되도록 함
    ```
    
- frontend에서의 페이지네이션
    
    ```jsx
    문제 : 모든 게시글을 select하여 frontend로 전달 후 페이지네이션을 구현하였으나 2페이지 선택 부터는 빨랐으나
    			첫페이지에서 모든 게시글을 가져오기에 성능이 떨어져 속도가 저하됨
    
    과정 : 
    		-> backend에서 findAndCount 함수를 통해 한 페이지에 가져올 데이터의 제한 갯수와 
    				이전 요청의 데이터 갯수를 계산하는 페이지네이션을 구현
    		-> 계산된 값으로 select 해서 가져오는 값의 범위를 축소시킴
    
    결과 : backend에서 구현한 페이지네이션을 적용한 결과, 속도가 향상되었음
    ```
    
- 좋아요 / 댓글 카운트
    
    ```jsx
    문제 : 초반에는 update 문을 사용하여 like + 1로 구현을 하였으나
    			많은 사용자 부하테스트 결과 count를 제대로 인식하지 못하는 현상이 발생함  
    			 => 동시성 문제
    
    결과 : typeorm의 createselectquery를 사용하여 좋아요 / 댓글 갯수를 세는 카운트를 구현
    ```
    
- 비로그인 유저
    
    ```jsx
    문제 : 비로그인 시 게시글, 리뷰 작성 시 글쓰기는 가능하지만 저장을 눌렀을 때 undefind 가 뜬다는 사용자 피드백
    
    과정 : backend에서 UseGuards를 활용하여 user정보를 반환하도록 설계
    
    결과 : 비로그인 시 axios get을 통해 유저 정보가 없을 시 로그인 페이지로 이동하게 구현
    ```
    
- 검색 기능 like `%${검색어}%`로 기능 구현하여 불필요한 정보가 불러와짐
    
    ```jsx
    문제 : like `%${검색어}%`을 이용하여 검색 기능 구현을 시도 하였으나 불필요한 정보가 불러와짐
    
    결과 : elasticsearch, kibana 도입으로 보다 빠르고 완벽한 검색 기능 구현
    ```
    
- elastic search → elastic search cloud로 변경
    
    ```jsx
    문제 : elastic search 도입하였으나 서버 사양 부족으로 로컬 elastic search 구현 불가
    
    과정 :
    		-> opensearch 를 사용하려 시도하였으나 비용적인 문제가 발생
    			 (상용 프로젝트일 경우 opensearch 가 효율적이라고 판단됨)
    		-> elastic search cloud 14일간 무료 평가판 제공됨
    
    결과 : elastic search cloud 로 변경하여 서버의 부담을 없앰    
    ```
    

## 🔎 주요 기능

## Description

[Nest](https://github.com/nestjs/nest) framework TypeScript starter repository.

## Installation

```bash
$ npm install
```

## Running the app

```bash
# development
$ npm run start

# watch mode
$ npm run start:dev

# production mode
$ npm run start:prod
```

## Test

```bash
# unit tests
$ npm run test

# e2e tests
$ npm run test:e2e

# test coverage
$ npm run test:cov
```

## Support

Nest is an MIT-licensed open source project. It can grow thanks to the sponsors and support by the amazing backers. If you'd like to join them, please [read more here](https://docs.nestjs.com/support).

## Stay in touch

- Author - [Kamil Myśliwiec](https://kamilmysliwiec.com)
- Website - [https://nestjs.com](https://nestjs.com/)
- Twitter - [@nestframework](https://twitter.com/nestframework)

## License

Nest is [MIT licensed](LICENSE).
