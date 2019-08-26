---
layout: post
title: 'Mybatis - ParamterType'
author: kjham.ham
date: 2019-08-23 15:30
tags: [swtech,java,mabatis,backend]
---

Mybatis의 XML을 작성할 때  
Single / Multiple ParamterType 사용법에 대해 기술해봅니다.  

## Single Parameter  

다음과 같이 Mapper 용 Interface를 선언하였습니다.  
~~~java
public interface HumanResourceSyncMapper {
    // Single Parameter
    List<HumanResourceSyncCommon> selectHumanResourceConfig(int syncTypeCd);

    // Multiple Parameter
    int insertHumanResourceConfig(HumanResourceSyncCommon humanResourceSyncCommon);
}
~~~

`_parameter`를 이용하여 조건문 `if`에서 활용이 가능하고,  
SQL 내에는 `#{id}`를 이용하여 Parameter를 호출하였습니다.  
~~~xml
<select id="selectHumanResourceConfig" resultType="com.example.dto.humanResourceSync.HumanResourceSyncCommon">
    -- 인사연동 설정 조회
    SELECT  hr_sync_type_cd AS syncTypeCd
            , hr_sync_cycle_val AS syncCycleVal
            , hr_sync_exe_time AS syncExeTimeVal
            , is_active AS isActive
    FROM    ${schema.grifas}.tbr_cnf_hr_sync
    WHERE   1=1
    <if test='_parameter > 0'>
        AND hr_sync_type_cd = #{id}
    </if>
</select>
~~~

## Multiple Parameter  

Interface는 위와 동일하고, 바로 XML 예를 보겠습니다.  

적절한 `DTO`를 정의 후 `#{변수이름}`을 이용하여 SQL에 넣으면 됩니다.  
~~~xml
<insert id="insertHumanResourceConfig" parameterType="com.example.dto.humanResourceSync.HumanResourceSyncCommon">
    -- 인사연동 설정정보 저장
    INSERT INTO
            ${schema.grifas}.tbr_cnf_hr_sync(hr_sync_type_cd, hr_sync_cycle_val, hr_sync_exe_time, is_active)
    VALUES (
        #{syncTypeCd}
        ,#{syncCycleVal}
        ,#{syncExeTimeVal}
        1
    )
</insert>
~~~