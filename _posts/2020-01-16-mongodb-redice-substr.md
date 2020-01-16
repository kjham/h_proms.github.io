---
layout: post
title: 'MongoDB String-List to Concat-String'
author: kjham.ham
date: 2020-01-16 15:30
tags: [swtech,java,database,mongodb,nosql]
##image: /files/covers/effectivejava.jpg
---

시간이 없어서.. 간략하게 적어봅니다.  
String으로 구성된 List를 하나의 필드안에 구분자를 주어 묶어야 하는 일이 생겼습니다.  

~~~js
{
        // DB 기준으로 테이블, 컬럼 리스트를 생성
        $group: {
                _id: "$dbNm",
                tableList: { $addToSet: "$tableNm" },
                columnList: { $addToSet: "$columnNm" }                
        },
        // reduce & concate 을 이용하여 List를 Concate String 으로 변환
        $project: {
                dbNm: "$_id",
                tableNm: { $reduce: {
                        input: "$tableList",
                        initialValue: "",
                        in: {
                        $concat: ["$$value", "_", "$$this"] }
                        }
                },
                columnNm: { $reduce: {
                        input: "$columnList",
                        initialValue: "",
                        in: { $concat: ["$$value", ",", "$$this"] }
                        }
                }
        },
        // 앞 단에 위치한 구분자 제거
        $project: {
                // 데이터베이스명
                dbNm: "$dbNm",
                // 테이블명 (목록)
                tableNm: { $substr: ["$tableNm", 1, -1] },
                // 칼럼명 (목록)
                columnNm: { $substr: ["$columnNm", 1, -1] }        
        }
}
~~~

특별한 문제 없이 결과가 잘 나옵니다.