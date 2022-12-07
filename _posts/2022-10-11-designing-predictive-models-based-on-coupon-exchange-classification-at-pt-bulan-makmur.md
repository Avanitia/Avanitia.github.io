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
However, this resulted in an intense competition between each competitors. To overcome this, the company targets the use of coupons to maintain customer satisfaction and loyalty. From January 2022 to February 2022, there’s a low e-coupon redemption rate in the company (~2%). Based on the preliminary study, it was found that the main cause lies in both the absence of a model that can predict the consumer coupon redemption decision and presence of factors that influence the decision. This research was conducted to build a predictive model about influencing factors for coupon exchange decisions through PT Bulan Makmur’s customer data. </p>

<h2>Research Methodology</h2>
<p align="justify">
This research used conceptual design of the engineering design (Dieter & Schimdt, 2013) as a base concept, followed by Cross-Industry Standard Process for Data Mining (CRISP-DM) to solve the problem. With that in mind, below is the flow used for this research.</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/49559301/206133910-ea382e84-5aed-48bf-b5b0-b144aaa7d9ed.png"/>
</p>

<h2>Research Analysis</h2>
<p align="justify">
In order to develop a predictive model, BigQuery SQL is used for data gathering, and then Python for data manipulation, visualization, and machine learning purposes. The details regarding the code itself won't be put inside this post, but you can enquire it to me using my contact information provided in the homepage. Below is the highlighted results of some of the visualizations and model. </p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/49559301/206136724-52016dd5-73f2-4cea-84bc-df3a788e8c6c.png">
</p>

<h2>Conclusion</h2>
<ol type = 1>
  <li>The best predictive model to predict customer behavior on coupon exchange is XGBOOST, with SMOTE-ENN for the imbalanced treatment and without any feature selection.</li>
  <li>The model has 90-95% prediction power, with 0.9 accuracy, 0.83 recall, 0.73 precision, and 0.77 F1 score.</li>
  <li>Discount, AOV (Average Order Value), and tenure are the some of the top variables which influence customers the most for coupon redemption. </li>
</ol>

<h2>Recommendation</h2>
  <ol type = 1>
  <li> Model scale-up for future campaign to improve voucher performance. </li>
  <li> Voucher rework to incentivize people who are predicted to not redeem to redeem </li>
  <li> Based on variables which influence model prediction the most, PT Bulan Makmur is recommended to: <ol type = 3>
    <li> Product improvement -> voucher section, general usability improvement </li>
    <li> Consumer segmentation based on average spending </li>
    <li> Incentive program for loyal customers, eg. loyalty program </li>
    <li> Offline marketing campaign </li> </li>
  
  
  



