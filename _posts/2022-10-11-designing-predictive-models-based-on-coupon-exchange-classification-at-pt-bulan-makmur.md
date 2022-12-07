## Designing Predictive Models Based on Coupon Exchange Classification at PT Bulan Makmur

<p align= "justify">
  In this post, I will share my data science project on how to apply machine learning into classification prediction of coupon exchange behaviour using PT Bulan Makmur as a case study (real name hidden since it's a sensitive information). Enjoy! </p>
  
<h2>Background</h2>

<p align="justify">
PT Bulan Makmur is a e-grocery startup company located in Indonesia. Like other companies engaged in the same field, PT Bulan Makmur is currently experiencing rapid development 
because of the COVID-19 pandemic. Recent study from L.E.K Consulting shown that COVID-19 pandemic has increased the value of e-grocery in Indonesia from 1 billion to 6 billion dollar in 2025 <a href="https://www.consultancy.asia/news/3941/covid-19-a-catalyst-for-growth-in-indonesias-e-grocery-market">(Consultancy Asia, 2021)</a>. We can also see Indonesia's E-grocery improvement as opposed to other SEA's nations based on Consultancy Asia study below. </p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/49559301/206113714-4ebd54ba-9bbb-4672-b87f-f19f75017cf6.png" width=600 height=450/>
</p>

<p align="justify">
However, this resulted in an intense competition between each competitors. To overcome this, the company targets the use of coupons to maintain customer satisfaction and loyalty. From January 2022 to February 2022, there’s a low e-coupon redemption rate in the company (~2%). Based on the preliminary study, it was found that the main cause lies in both the absence of a model that can predict the consumer coupon exchange decision and presence of factors that influence the decision. This research was conducted to build a predictive model about influencing factors for coupon exchange decisions through PT Bulan Makmur’s customer data. </p>

<h2>Research Methodology</h2>
This research used engineering design (Dieter & Schimdt, 2013) as a base concept, which has 5 important 
