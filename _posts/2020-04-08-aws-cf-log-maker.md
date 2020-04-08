---
layout: post
title: 'AWS CloudFront Log 만들기'
author: kjham.ham
date: 2020-04-08 16:32
create-date: 2020-04-08 16:32
tags: [swtech,cloud,aws,cloudfront,java]
##image: /files/covers/effectivejava.jpg
comments: true
---

# AWS CloudFront Log 만들기

CF Log 테스트 자료를 만들기 위해 동일한 필드로 구성하여 생성할 수 있습니다.
천만건 까지 생성해봤고, 로는 BearTrail 같은 상용도구로 열어서 볼 수 있습니다.
라인 수가 너무 많으면 일반적인 에디트 도구 (notepad++) 로 열리지 않습니다.

언어는 `JAVA` 입니다.

로그를 생성하는 `CfLogMakeService` 클래스 정의입니다.  
~~~java
import java.io.File;
import java.io.FileWriter;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Calendar;
import java.util.List;
import java.util.Random;

public class CfLogMakeService {

    private String HEADER = "#Version: 1.0\r\n#Fields: date time x-edge-location sc-bytes c-ip cs-method cs(Host) cs-uri-stem sc-status cs(Referer) cs(User-Agent) cs-uri-query cs(Cookie) x-edge-result-type x-edge-request-id x-host-header cs-protocol cs-bytes time-taken x-forwarded-for ssl-protocol ssl-cipher x-edge-response-result-type cs-protocol-version fle-status fle-encrypted-fields c-port time-to-first-byte x-edge-detailed-result-type sc-content-type sc-content-len sc-range-start sc-range-end\r\n";

	// 로그생성 기준일시, 초당 로그 건수
    public void writeLogFile(Calendar standardCal, Integer logCountPerSec) {
        try {
            standardCal.set(Calendar.MINUTE, 0);
            standardCal.set(Calendar.SECOND, 0);

            Calendar now = Calendar.getInstance();
            now.setTime(standardCal.getTime());
            Calendar tomorrow = Calendar.getInstance();
            tomorrow.setTime(standardCal.getTime());
            tomorrow.add(Calendar.HOUR_OF_DAY, 1);

            String filePath = "C:/logs";
            File dir = new File(filePath);
            if(!dir.isDirectory()) {
                dir.mkdir();
            }

            StringBuffer cfId = new StringBuffer();
            cfId.append(filePath);
            cfId.append("/");
            for(int i = 0; i < 13; i ++) {
                cfId.append((char) ((int) (new Random().nextInt(26)) + 65));
            }
            cfId.append(".");
            cfId.append( new SimpleDateFormat("yyyy-MM-dd-HH").format(now.getTime()));
            cfId.append(".");
            cfId.append(new Random().nextInt(79999999) + 10000000);
            File file = new File(cfId.toString());
            FileWriter fw = new FileWriter(file);
            fw.write(HEADER);

            long totalLine = 0L;
            while(now.before(tomorrow)) {

                SimpleDateFormat sdfDate = new SimpleDateFormat("yyyy-MM-dd");
                SimpleDateFormat sdfTime =  new SimpleDateFormat("HH:mm:ss");
                List<String> cfLogInfoList = makeCfLogInfo(sdfDate.format(now.getTime()), sdfTime.format(now.getTime()), logCountPerSec);

                for(String cfLogInfo : cfLogInfoList) {
                    fw.write(cfLogInfo);
                }
                totalLine += cfLogInfoList.size();
                System.out.println("### " + new SimpleDateFormat("yyyy-MM-dd-HH:mm:ss").format(now.getTime()) + " : " + cfLogInfoList.size() +  " Write Success !");
                now.add(Calendar.SECOND, 1);
            }
            fw.flush();
            System.out.println("### Work Complete. File Full Path : " + cfId.toString().toString());
            System.out.println("### Total line : " + totalLine);
        } catch(Exception ex) {
            ex.printStackTrace();
        }
    }


    private List<String> makeCfLogInfo(String date, String time, Integer logCountPerSec) {
        List<String> cfLogInfoList = new ArrayList<>();

        StringBuffer distId = new StringBuffer();
        for(int i = 0; i < 15; i ++) {
            distId.append((char) ((int) (new Random().nextInt(26)) + 97));
        }

        for(int i = 0; i < logCountPerSec; i ++) {
            StringBuilder sb = new StringBuilder();
            sb.append(date); sb.append("\t");
            sb.append(time); sb.append("\t");
            sb.append("ICN55-XX"); sb.append("\t");
            sb.append(String.format("%d", 3000 + new Random().nextInt(47000))); sb.append("\t");
            sb.append("218.xxx.xxx.106"); sb.append("\t");
            sb.append("GET"); sb.append("\t");
            sb.append(distId.toString() + ".cloudfront.net"); sb.append("\t");
            sb.append("/kjham_test.png"); sb.append("\t");
            sb.append("200"); sb.append("\t");
            sb.append("-"); sb.append("\t");
            sb.append("Mozilla/5.0%20X(Windows%20NT%2010.0;%20Win64;%20x64)%20AppleWebKit/537.36%20(KHTML,%20like%20Gecko)X%20Chrome/80.0.3987.149%X20Safari/537.36"); sb.append("\t");
            sb.append("-"); sb.append("\t");
            sb.append("-"); sb.append("\t");
            sb.append(new Random().nextInt(100) != 0 ? "Hit" : "Miss"); sb.append("\t");
            sb.append("canpnsbL0TTJxaQIxNp6h0XzWj0gJax8m67CZwu45aX0xfTkRDIsXw=="); sb.append("\t");
            sb.append(distId.toString() + ".cloudfront.net"); sb.append("\t");
            sb.append("https"); sb.append("\t");
            sb.append(String.format("%d", 10 + new Random().nextInt(990))); sb.append("\t");
            sb.append(String.format("%f", 0.001 + new Random().nextInt(90) / 1000d)); sb.append("\t");
            sb.append("-"); sb.append("\t");
            sb.append("TLSv1.2"); sb.append("\t");
            sb.append("ECDHX-RSA-AES138-XCM-SHA256"); sb.append("\t");
            sb.append(new Random().nextInt(100) != 0 ? "Hit" : "Miss"); sb.append("\t");
            sb.append("HTTP/2.0"); sb.append("\t");
            sb.append("-"); sb.append("\t");
            sb.append("-"); sb.append("\t");
            sb.append(String.format("%d", 5000 + new Random().nextInt(45000))); sb.append("\t");
            sb.append(String.format("%f", 0.001 + new Random().nextInt(90) / 1000d)); sb.append("\t");
            sb.append(new Random().nextInt(100) != 0 ? "Hit" : "Miss"); sb.append("\t");
            sb.append("image/png"); sb.append("\t");
            sb.append(String.format("%d", 3000 + new Random().nextInt(47000))); sb.append("\t");
            sb.append("-"); sb.append("\t");
            sb.append("-"); sb.append("\r\n");
            cfLogInfoList.add(sb.toString());
        }

        return cfLogInfoList;
    }
}
~~~

로그를 생성하기 위해 호출하는 `MainApp` 클래스 정의입니다.  
~~~java
import java.util.Calendar;

public class MainAPp {

    public static void main(String[] args) {

        CfLogMakeService CfLogMakeService = new CfLogMakeService();

        Calendar calendar = Calendar.getInstance();
        calendar.set(Calendar.DATE, 8);
        calendar.set(Calendar.HOUR_OF_DAY, 11);
        // 현재일시에서 8일, 11시를 세팅하여 전달, 초당 건수는 2778건
        CfLogMakeService.writeLogFile(calendar, 2778);

        // ex
        // countPerSec 100 : 36만 건 (3600 * 100) (약 155mb)
        // countPerSec 277 : 100만 건
    }
}
~~~