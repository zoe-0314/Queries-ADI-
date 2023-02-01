# Queries (ADI)

> 분석 업무를 진행하면서 사용했던 쿼리   
> These are the queries I used during online ad data analysis work.   

- MySQL(MariaDB)   


### - Facebook data check  
> 운영중인 업체의 페이스북 광고 성과를 확인하는 쿼리
> Queries to check the Facebook advertising performance of our clients
```SQL
select       dayname(tsc)                                          as 'dayname',
             xmm,
             xmd,
             sitePk,
             f_name,
             account_id,
             camp_name,
             adset_name,
             name,
             #substring_index(replace(name, ' - 사본', ''), '_', 1)           as ad_sort,            #수기 광고별 분류
          /*case
              when camp_name like '%리타게팅%' then '리타겟팅'
              when camp_name like '%유입%' then '유입목적'
              when camp_name like '%도달%' then '도달및빈도'
              when camp_name like '%전환%' then '전환목적'
              end                                       as camp_sort,
           */
             sum(visit)                                                      as landing_page,
             round(sum(adfee / 2), 0)                                        as adfee,
             round(sum(imp / 2), 0)                                          as imp,
             round(sum(outbound_click / 2), 0)                               as visit,
             sum(visit)                                                      as visit_fb_web_log,
             sum(link_click / 2)                                             as link_click,
             ROUND(sum(adfee) / sum(imp) * 1000, 0)                          as CPM,
             round(sum(adfee / 2) / sum(outbound_click / 2), 0)              as CPC,
             round((sum(outbound_click / 2) / sum(imp / 2)) * 100, 2)        as click_rate,
             round(SUM(order_click_7d) / sum(outbound_click / 2) * 100, 2)   as CVR_7_C,
             sum(order_click_1d)                                             as order_1_C,
             sum(order_click_7d)                                             as order_7_C,
             sum(sales_click_1d)                                             as sales_1_C,
             sum(sales_click_7d)                                             as sales_7_C,
             round(sum(sales_click_1d) / sum(adfee / 2) * 100, 0)            as ROAS_1_C,
             round(sum(sales_click_7d) / sum(adfee / 2) * 100, 0)            as ROAS_7_C,
             sum(addToCart_click_1d)                                         as addToCart_click_1d,
             sum(addToCart_click_7d)                                         as addToCart_click_7d,
             #sum(addToCart_click_28d)                                as addToCart_click_28d,
             #sum(addToCart_view_1d)                                   as addToCart_view_1d,
             #sum(addToCart_view_7d)                                   as addToCart_view_7d,
             #sum(addToCart_view_28d)                                 as addToCart_view_28d,
             sum(complRegist_click_1d)                                       as complRegist_click_1d,
             sum(complRegist_click_7d)                                       as complRegist_click_7d,
             #sum(complRegist_click_28d)                              as complRegist_click_28d,
             #sum(complRegist_view_1d)                                 as complRegist_view_1d,
             #sum(complRegist_view_7d)                                 as complRegist_view_7d
             #sum(complRegist_view_28d)                               as complRegist_view_28d
             #sum(addPay_view_1d)                                      as addPay_view_1d,
             #sum(addPay_view_7d)                                      as addPay_view_7d,
             sum(addPay_click_1d)                                            as addPay_click_1d,
             sum(addPay_click_7d)                                            as addPay_click_7d,
             sum(searches_click_1d)                                          as searches_click_1d,
             sum(searches_click_7d)                                          as searches_click_7da,
             #sum(searches_view_1d)                                    as searches_view_1d,
             #sum(searches_view_7d)                                    as searches_view_7d,
             sum(leads_click_1d)                                             as leads_click_1d,
             sum(leads_click_7d)                                             as leads_click_7d,
             #sum(leads_view_1d)                                       as leads_view_1d,
             #sum(leads_view_7d)                                       as leads_view_7d
             sum(contentView_click_1d)                                       as cont_View_1d_C,     #컨텐츠조회 클릭 후 1일
             sum(contentView_click_7d)                                       as cont_View_7d_C,     #컨텐츠조회 클릭 후 7일
             round((sum(adfee / 2)) / sum(contentView_click_1d), 0)          as cont_View_1d_C_AVG, #컨텐츠조회당 비용_클릭 후 1일
             round((sum(adfee / 2)) / sum(contentView_click_7d), 0)          as cont_View_7d_C_AVG, #컨텐츠조회당 비용_클릭 후 7일
             round(sum(contentView_click_1d) / sum(outbound_click / 2), 2)   as cont_View_1d_Per,   # 평균 아웃바운드클릭당 컨텐츠조회수_클릭후1일
             round(sum(contentView_click_7d) / sum(outbound_click / 2), 2)   as cont_View_7d_Per    # 평균 아웃바운드클릭당 컨텐츠조회수_클릭후7일
              ,
             sum(contentView_click_7d) - sum(contentView_click_1d)           as revisit_contentView,
             round(((sum(contentView_click_7d) - sum(contentView_click_1d)) / sum(contentView_click_1d)) * 100,
                   0)                                                        as revisit_rate
              ,
             sum(outbound_click) / 2                                         as outbound click,
             sum(link_click) / 2                                             as link click,
             sum(inline_click) / 2                                           as inline click,
             sum(click) / 2                                                  as click,
             sum(adfee) / sum(complRegist_click_7d)                          as join_7d_avg,
             sum(reach)                                                      as reach,
             (sum(contentView_click_1d) / sum(fb_fb_log.outbound_click)) / 2 as '유입당 상품조회수',
             sum(outbound_click) / 2,
             sum(contentView_click_1d)
             #pk
             #,round(sum(contentView_click_1d)/sum(outbound_click /2) - sum(contentView_click_1d)/sum(outbound_click /2)%0.1,1) as floor_cont_View_1 
             # 평균 아웃바운드클릭당 컨텐츠조회수_1일_floor
             #,round(sum(contentView_click_7d)/sum(outbound_click /2) - sum(contentView_click_7d)/sum(outbound_click /2)%0.1,1) as floor_cont_View_7 
             # 평균 아웃바운드클릭당 컨텐츠조회수_7일_floor
             # sum(order_click_7d)                                           as order_7_C,
             # round(sum(sales_click_7d) / sum(adfee / 2) * 100, 0)          as ROAS_7_C,
             # sum(order_view_7d)                                            as order_7_v,
             # round(sum(sales_view_7d) / sum(adfee / 2) * 100, 0)           as ROAS_7_v,
             # sum(outbound_click)
      from _web_log

           #기간 설정
      WHERE #(date_format(tsc, "%Y%m%d") between '20191201' AND '20191223')
          (xmm * 100 + xmd >= 220622)
        AND (xmm * 100 + xmd <= 220623)
		
#fb_web_log.tsc between date_add(now(),interval -1 week) and now()

        #계정 단위 성과
        /*and sitePk in (select adsites.sitePk
                       from adsites
                                left join sites on adsites.sitePk = sites.pk
                       where sites.status = 1
                         and adsites.status = 'active'
                         and svcPk = 33
            )*/
        and sitePk=

      GROUP BY sitePk
             #with rollup
			 
             # 월별
             , xmm

             #일별
             , xmd

             #주별
             #, DATE_FORMAT(tsc, "%u")
             #,xmm, xmd
#having imp>1000
      order by f_name, xmm, xmd
```

### - keyword ad data check    
> keyword_log, cckeyword_log, AdSummaryTotal 각 세 테이블에 키워드 데이터가 제대로 수집이 되었는지 확인하는 쿼리   
> Query to check whether keyword data has been properly collected in each of three tables: keyword_log, cckeyword_log, and AdSummaryTotal   


```SQL
select @tsc1 := '2022-06-29 00:00:00', @tsc2 := '2022-06-29 23:59:59';

SELECT *
FROM (select tsc,
             krName,
             sitePk,
             adsitePk,
             sum(adfee)                                                     as adfee,
             sum(keyword_log.order_passive) + sum(keyword_log.order_active) as order_T,
             if(adfee > 0, 'KeywordAd', 'KeywordAd')
                                                                            AS ServiceType,
             if(adfee > 0, 'keyword_log', 'keyword_log')                    AS tableName
      from keyword_log
               left join adsites on adsitePk = adsites.pk
               left join sites on adsites.sitePk = sites.pk
      where adsitePk in()
        and tsc between @tsc1 and @tsc2
        and srvPk = 7
      group by adsitePk
      UNION ALL
      select tsc,
             krName,
             sitePk,
             adsitePk,
             sum(adfee)                                                                 as adfee,
             sum(adi_cckeyword_log.order_passive) + sum(adi_cckeyword_log.order_active) as order_T,
             if(adfee > 0, 'KeywordAd', 'KeywordAd')                                    AS ServiceType,
             if(adfee > 0, 'cckeyword_log', 'cckeyword_log')                    AS tableName
      from adi_cckeyword_log
               left join adsites on adsitePk = adsites.pk
               left join sites on adsites.sitePk = sites.pk
      where adsitePk in()
        and tsc between @tsc1 and @tsc2
        and srvPk = 7
      group by adsitePk
      UNION ALL
      select tsc,
             krName,
             sitePk,
             adsitePk,
             sum(adfee)                                        as adfee,
             sum(orderActive) + sum(orderPassive)              as order_T,
             if(adfee > 0, 'KeywordAd', 'KeywordAd')           AS ServiceType,
             if(adfee > 0, 'AdSummaryTotal', 'AdSummaryTotal') AS tableName
      from AdSummaryTotal
      where adsitePk in()
        and tsc between @tsc1 and @tsc2
        and AdSummaryTotal.svcPk = 7
        and AdSummaryTotal.category = 1
        AND srvPk not in (14)
      group by adsitePk
      UNION ALL
      select tsc,
             krName,
             sitePk,
             adsitePk,
             sum(adfee)                                                           as adfee,
             sum(Shopping_log.order_passive) + sum(Shopping_log.order_active) as order_T,
             if(adfee > 0, 'ShopingAd', 'ShopingAd')                              AS ServiceType,
             if(adfee > 0, 'ccshopping_log', 'ccshopping_log')                    AS tableName
      from Shopping_log
               left join adsites on Shopping_log.adsitePk = adsites.pk
               left join sites on adsites.sitePk = sites.pk
      where adsitePk in()
        and tsc between @tsc1 and @tsc2
        and srvPk = 7
      group by adsitePk
      UNION ALL
      select tsc,
             krName,
             sitePk,
             adsitePk,
             sum(adfee)                                                                   as adfee,
             sum(ccshopping_log.order_passive) + sum(ccshopping_log.order_active) as order_T,
             if(adfee > 0, 'ShopingAd', 'ShopingAd')                                      AS ServiceType,
             if(adfee > 0, 'ccshopping_log', 'ccshopping_log')                    AS tableName
      from adi_ccshopping_log
      where adsitePk in()
        and tsc between @tsc1 and @tsc2
        and srvPk = 7
      group by adsitePk
      UNION ALL
      select tsc,
             krName,
             sitePk,
             adsitePk,
             sum(adfee)                                        as adfee,
             sum(orderActive) + sum(orderPassive)              as order_T,
             if(adfee > 0, 'ShopingAd', 'ShopingAd')           AS ServiceType,
             if(adfee > 0, 'AdSummaryTotal', 'AdSummaryTotal') AS tableName
      from AdSummaryTotal
      where adsitePk in ()
        and tsc between @tsc1 and @tsc2
        and AdSummaryTotal.svcPk = 7
        and AdSummaryTotal.category = 2
        AND srvPk not in (14)
      group by adsitePk) AS B
GROUP BY tableName, krName, adfee
ORDER BY krName DESC, ServiceType ASC, tableName ASC, adfee ASC;
```

> 운영중인 업체의 키워드 광고 성과를 확인하는 쿼리
> Queries to check the keyword advertising performance of our clients

```SQL
select *
from (SELECT cckeyword_log.tsc                                                                     as tsc,
             xmm,
             xmd,
             krName,
             sitePk,
             cckeyword_log.srvPk,
             #cckeyword_log.keyword                                                                 as keyword_pk,
             keyword_worddb.keyword,
             device,
             m.regTm,
             #groupName,
          /*case
             when groupName like '%(주력시간)' then '주력키워드_주력시간'
             when groupName like '%(비주력시간)' then '주력키워드_비주력시간'
               end                   as group_div,*/
             round(sum(rankAvg * cckeyword_log.imp) / sum(cckeyword_log.imp), 2)               as rankAvg_,
             #rankAvg,
             #floor(round(sum(rankAvg * cckeyword_log.imp) / sum(cckeyword_log.imp), 2) -  (round(sum(rankAvg * cckeyword_log.imp) / sum(cckeyword_log.imp), 2)%1))     as rank_floor,
             sum(adi_cckeyword_log.adfee)                                                              AS adfee,
             rank() over (order by adi_cckeyword_log.adfee desc )                                      as adfee_rank,
             percent_rank() over (order by adi_cckeyword_log.adfee desc )                              as adfee_rank_percent,
             #floor(sum(adi_cckeyword_log.adfee) - round(sum(cckeyword_log.adfee) % 10000))         as adfee_floor,
             sum(cckeyword_log.imp)                                                                AS imp,
             sum(cckeyword_log.visit_active)                                                       AS click,
             round(sum(cckeyword_log.visit_active) / SUM(cckeyword_log.imp) * 100, 2)          AS CTR,
             ROUND(sum(cckeyword_log.adfee) / sum(cckeyword_log.visit_active), 0)              AS CPC,
             sum(cckeyword_log.order_active)                                                       AS order_A,
             sum(cckeyword_log.order_passive)                                                      AS order_P,
             sum(cckeyword_log.order_active) + sum(cckeyword_log.order_passive)                AS order_T,
             round(sum(cckeyword_log.order_active) / SUM(cckeyword_log.visit_active) * 100, 2) AS cvr_A,
             round(
                             (sum(cckeyword_log.order_active) + sum(cckeyword_log.order_passive)) /
                             SUM(cckeyword_log.visit_active)
                         * 100, 2)                                                                     AS cvr_T,
             sum(cckeyword_log.sales_active)                                                       AS sales_A,
             sum(cckeyword_log.sales_active) + sum(cckeyword_log.sales_passive)                AS sales_T,
             round(sum(cckeyword_log.sales_active) / sum(cckeyword_log.adfee) * 100, 0)        AS ROAS_A,
             round((sum(cckeyword_log.sales_active) + SUM(cckeyword_log.sales_passive)) /
                   sum(cckeyword_log.adfee) * 100, 0)                                              AS ROAS_T

             sum(pv)                                                                                   as PV,
             round(sum(bounced) / sum(visit_active) * 100, 0)                                          as bounced_rate
             #     floor((sum(bounced) / sum(visit_active) * 100) -
             #          ((sum(bounced) / sum(visit_active) * 100) % 10))                   as bouncedRate_floor 

      FROM cckeyword_log
      WHERE
      cckeyword_log.pk>75000000
      #krName IN ("")
        and sitePk in (7)
        #미디어 계정(7), 스크립트 성과 확인(14)
        AND adi_cckeyword_log.srvPk = 7
        #자상호 제외 쿼리, 그룹별로 볼 때 자상호를 포함하여 보고 싶으면 주석처리를 해야 됩니다
        #and keyword_worddb.keyword not  in(select keyword_self.keyword from keyword_self where a.sitePk = keyword_self.sitePk group by keyword_self.pk)
       
        #디바이스 구분
        #and device = 'mobile'

        #그룹 제외하거나 특정 그룹만 선택할 때 사용해요
        #and (name like '%자상호%' or groupName like '%자상호%')
        #and (name  like '%%' or  name like '%%')

        #특정 키워드 선택
        #and keyword_worddb.keyword regexp '|'
        #and keyword_ID in ('')

        #기간선택
        #AND cckeyword_log.tsc BETWEEN "2020-02-20 00:00:00" AND "2020-02-20 23:59:59"
        AND (xmm * 100 + xmd >= 220401)
        AND (xmm * 100 + xmd <= 220429)
        #and xmm in (2011) and  xmd in (10)

#order by device, tsc asc  #limit 30
     ) as temp
#where PV is null
#GROUP BY krName #, adfee_floor
#having adfee<260000#, groupName  #having adfee>0 # , device #having order_T>1
```
