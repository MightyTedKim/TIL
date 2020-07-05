ElasticSearch 기본 개념 정리

![](https://images.velog.io/images/deet1107/post/d7a3d04d-26e6-4886-857e-e9e990548873/image.png)

ELK 는 흔히들 말하는 elasticsearch, logstash, kibana에요
최근에는 filebeat가 추가되면서 elk stack으로 불리기도 해요

여기서는 elk 중에서 elasticsearch를 중점적으로 보려고 해요
# 참고 자료

- 책 
    - 엘라스틱서치 실무 가이드 : 한글 검색 시스템 구축부터 대용량 클러스터 운영까지
    - 한글 형태소 분석과 suggest api 등을 엄청 자세히 설명해주십니다.
      진짜 입문하기 너무너무너무 좋은 책이에요
      ![](https://images.velog.io/images/deet1107/post/01421dcd-7db5-4552-9812-f4e598a8db29/image.png)

- 아프리카 TV elasticsearch 도입기
    - 내가 했던 모든 고민 포함, 소름
    - [https://www.sosconhistory.net/soscon2018/pdf/day2_1000_2.pdf](https://www.sosconhistory.net/soscon2018/pdf/day2_1000_2.pdf)
- 공식 문서
    - 공식 문서 엄청 잘되어 있음
    - [https://www.elastic.co/guide/en/elasticsearch](https://www.elastic.co/guide/en/elasticsearch)
- 좋은 블로그
    - 정말 깔끔하게 설명되어 있는 블로그
    - [https://velog.io/@jakeseo_me/번역-엘라스틱서치와-키바나-실용적인-소개서](https://velog.io/@jakeseo_me/%EB%B2%88%EC%97%AD-%EC%97%98%EB%9D%BC%EC%8A%A4%ED%8B%B1%EC%84%9C%EC%B9%98%EC%99%80-%ED%82%A4%EB%B0%94%EB%82%98-%EC%8B%A4%EC%9A%A9%EC%A0%81%EC%9D%B8-%EC%86%8C%EA%B0%9C%EC%84%9C)
- 2019, Elasticsearch를 통한 Full-text 및 로그 분석으로 데이터에 대한 인사이트 키우기 - 안효빈 솔루션즈 아키텍트(AWS)
	- 귀에 쏙쏙들어오는 40분짜리 설명
    - [https://www.youtube.com/watch?v=TXvsMotkD1k&t=91s](https://www.youtube.com/watch?v=TXvsMotkD1k&t=91s)

# 들어가기 전에

분석기로 쪼개서 검색, Analyzer 가 중요

- DB 아님
    - 검색에 특화
    - 오픈소스
- Term 단위로 쪼개고, 검색한다.
    - like 검색과 다르다
    - analyzer 가 중요
- Index
    - RDB의 테이블 개념
    - analyzer 등 설정
- 루씬
    - 루씬을 동시에 돌리기 때문에 성능이 높아짐
    - Elastic : Lucene : Segment(데이터) = 1: N : N

![](https://images.velog.io/images/deet1107/post/8c757851-efda-4392-816b-4f5ce8f472ff/Untitled%201.png)


# 설치

Window가 편함, docker/linux 모두 가능

- ElasticSearch
    - [https://www.elastic.co/guide/en/elasticsearch/reference/7.6/install-elasticsearch.html](https://www.elastic.co/guide/en/elasticsearch/reference/7.6/install-elasticsearch.html)
    
> 6.x 에서는 설정안하고, network.host만 풀어주면 가능했던거같은데, 7.7로 테스트하고 있는 지금은 node-name도 주석 풀어줘야하는 것 같음
node.name: node-1
network.host: "0.0.0.0"
discovery.seed_hosts: ["127.0.0.1", "[::1]"]
cluster.initial_master_nodes: ["node-1"]

- Kibana
    - [https://www.elastic.co/guide/en/kibana/7.6/install.html](https://www.elastic.co/guide/en/kibana/7.6/install.html)
    - config 폴더에서 elasticsearch url 주석을 풀어줌
![](https://images.velog.io/images/deet1107/post/77abd529-1052-46bf-8d7e-a14c7539c227/Untitled%203.png)
> server.port: 5601
server.host: "0.0.0.0"
$ elasticsearch.hosts: ["http://192.168.4.103:9200"] 
$ 다른 PC에서 접근할라면 ip 명시해줘야함
elasticsearch.hosts: ["localhost:9200"]

- pom.xml

    ```jsx
    <!-- https://mvnrepository.com/artifact/org.elasticsearch.client/elasticsearch-rest-high-level-client -->
     		<dependency>
    		    <groupId>org.elasticsearch.client</groupId>
    		    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    		    <version>7.6</version>
    		</dependency>
    ```

# 실습

- index
    - analyzer
- search
    - text / keyword
- Rest API
    - Java-High level Rest Client
    - json 과 동일

---

# 예시

- 참고 서적 : 엘라스틱서치 실무 가이드 한글 검색 시스템 구축부터 대용량 클러스터 운영까지
> 어짜피 따라하지 않을거고, 공식 doc에 좋은 예시들이 너무 많아서
간단한 눈팅용으로 정리 ㅋㅋ

## CRUD

- 방식이 다양함. 아래는 가장 기본적인 예시

```jsx
DELETE movie

PUT /movie
{
  "settings":{
    "number_of_shards":3,
    "number_of_replicas":2
  },
  "mappings":{
    "properties":{
       "movieCd": {"type":"keyword"},
       "movieCom": {"type":"keyword"},
       "movieNm": {"type":"text"},
       "movieDesc": {"type":"text"},
       "openDt":{"type":"date"},
       "movieScore":{"type":"integer"}
    }
  }
}

POST /movie/_doc/1
{
  "movieCd": "0001", 
  "movieCom":"Marvel",
  "movieNm":"아이언맨1",
  "movieDesc":"내가 제일 좋아하는 영화",
  "openDt":"2008-04-30",
  "movieScore":"9.0"
}

POST /movie/_doc/2
{
  "movieCd": "0002", 
  "movieCom":"Marvel", 
  "movieNm":"아이언맨2",  
  "movieDesc":"내가 제일 좋아하는 두번째 영화",
  "openDt":"2010-04-29",
  "movieScore":"7.0"
}

POST /movie/_doc/3
{
  "movieCd": "0004",  
  "movieCom":"DC", 
  "movieNm":"원더우먼",  
  "movieDesc":"내가 좋아하는 DC영화",
  "openDt":"2017-04-25",
  "movieScore":"8.0"
}
```

## 검색

- [https://www.elastic.co/guide/en/elasticsearch/reference/7.6/multi-fields.html](https://www.elastic.co/guide/en/elasticsearch/reference/7.6/multi-fields.html)

```jsx
GET /movie/_doc/1

GET movie/_search?q=movieCd:0001

GET /movie/_search
{
  "query": { "match": { "movieCd": "0001" } }
}

GET /movie/_search
{
  "query":{ "match":{"movieDesc":"영화 두번째"} }
}

GET movie/_search
{
  "query": {
    "range": {
      "openDt": {
          "gte": "2017-04-24",
          "lte": "2017-04-26"
      }
    }
  }
}

GET movie/_search
{
  "query": {
    "range": {
      "openDt": {
          "gte": "2010",
          "lte": "2017-04-26"
      }
    }
  }
}

GET movie/_search
{
  "aggs": {
    "movieCom": {
      "terms": {
        "field": "movieScore" 
      }
    }
  }
}

GET movie/_search
{
  "query": {
    "match": {
      "movieCom": "Marvel" 
    }
  },
  "sort": {
    "openDt": "asc" 
  },
  "aggs": {
    "movieCom": {
      "terms": {
        "field": "movieScore" 
      }
    }
  }
}
```

## 분석기

- Text를 이용해 전문 검색하려면, 어떻게 쪼개는지가 중요
- tokenizer → filter → analyzer
- [https://www.elastic.co/guide/en/elasticsearch/reference/7.6/specify-analyzer.html](https://www.elastic.co/guide/en/elasticsearch/reference/7.6/specify-analyzer.html)

```jsx
POST _analyze
{
  "analyzer": "standard",
  "text":"캐리비안의 해적"
}

POST movie_analyzer/_analyze
{
  "analyzer": "whitespace",
  "text":"Chamber of Secrets"
}

GET _analyze
{
  "tokenizer": "standard",
  "filter": [ "ngram" ],
  "text": "아이언맨"
}

```

- index에 분석기 넣기(Ngram)

```jsx
PUT ngram_example
{
  "settings": {
    "analysis": {
      "analyzer": {
        "standard_ngram": {
          "tokenizer": "standard",
          "filter": [ "ngram" ]
        }
      }
    }
  }
}

POST ngram_example/_analyze
{
  "text":"아이언맨",
  "analyzer": "standard_ngram"
}

PUT ngram_custom_example
{
  "settings": {
    "index": {
      "max_ngram_diff": 2
    },
    "analysis": {
      "analyzer": {
        "default": {
          "tokenizer": "whitespace",
          "filter": [ "3_5_grams" ]
        }
      },
      "filter": {
        "3_5_grams": {
          "type": "ngram",
          "min_gram": 3,
          "max_gram": 5
        }
      }
    }
  }
}

POST ngram_custom_example/_analyze
{
  "text":"아이언맨짱"
}

PUT /ngramtest
{
  "settings": {
    "index":{
      "number_of_shards" : 3,
      "number_of_replicas": 1,
      "max_ngram_diff": 49
    },
    "analysis": {
        "analyzer": {
          "ngram_analyzer":{
            "type":"custom",
            "tokenizer":"ngram_tokenizer",
            "filter":["lowercase","trim"]
          }
      },
      "tokenizer":{
        "ngram_tokenizer":{
          "type":"ngram",
          "min_gram":"1",
          "max_gram":"50",
          "token_chars":[
            "letter",
            "digit",
            "punctuation",
            "symbol"
            ]
        }
      }
    }
  }
}

PUT ngramtest/_mapping
{
 "properties": {
    "col1": {
      "type": "text"
    },
    "col2": {
      "type": "text",
      "analyzer":"standard",
      "search_analyzer":"ngram_analyzer"
    },
    "col3": {
      "type": "text",
      "analyzer":"ngram_analyzer",
      "search_analyzer":"standard"
    }
  }
}

POST ngramtest/_analyze
{
  "text":"내가 제일 좋아하는 영화",
  "analyzer": "standard"
}

POST ngramtest/_analyze
{
  "text":"내가 제일 좋아하는 영화",
  "analyzer": "ngram_analyzer"
}

POST /ngramtest/_doc/1
{
  "col1":"내가 제일 좋아하는 영화",
  "col2":"내가 제일 좋아하는 영화",
  "col3":"내가 제일 좋아하는 영화"
}

GET /ngramtest/_search
{
  "query":{ "match":{"col1":"하는"} }
}

GET /ngramtest/_search
{
  "query":{ "match":{"col2":"하는"} }
}

GET /ngramtest/_search
{
  "query":{ "match":{"col3":"하는"} }
}
```
