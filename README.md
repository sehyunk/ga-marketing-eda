

# GA 샘플데이터 마케팅 분석

- 빅쿼리 bigquery-public-data에는 ga데이터를 빅쿼리에서 다뤄볼 수 있는 샘플 데이터가 있습니다.

- 해당 샘플 데이터를 빅쿼리를 이용해서 추출하고

- 파이썬으로 불러와 채널별, 일자별 분석을 하였습니다.

  

## 빅쿼리로 데이터 추출

```sql
# 날짜, utm소스, utm매체
select date, trafficSource.source, trafficSource.medium,
		# 방문자 수 카운트
    count(d.visitId) as visit, 
    # 거래 수 카운트
    count(transaction.transactionId) as purchase,
    # 매출 합계, 애널리틱스로 전달될 때 10의6승으로 곱해져서 전달되므로 반대로 나눠준다.
    sum(prod.ProductRevenue)/power(10, 6) as revenue,
# ga데이터는 array형태로 되어있기 때문에 unnest해줘야 조회할 수 있음
from `bigquery-public-data.google_analytics_sample.ga_sessions_*` as d
		# hits array 풀기
    left join unnest(hits) as hits
    # 매출 집계를 위해 product 풀기
    left join unnest(hits.product) as prod
# 7월 데이터 필터링
where _table_suffix between  '20170701' and '20170731'
#  날짜, utm소스, utm매체로 그룹바이
group by date, trafficSource.source, trafficSource.medium
# 날짜로 소팅
order by date
```



## 판다스로 채널별 일자별 분석

1. 데이터 불러오기
2. 전처리
3. 마케팅 채널 별 분석
   - 인사이트
4. 일자 별 분석
   - 인사이트

[nb 뷰어에서 보기](https://nbviewer.jupyter.org/github/sehyunk/ga-marketing-eda/blob/master/ga-marketing-eda.ipynb)


