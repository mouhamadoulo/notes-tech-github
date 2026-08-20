# 🧠 System Design — Roadmap complète pour Software Engineer

Voici la version complète, organisée en **23 catégories**, avec une illustration pour chaque grande notion, puis les concepts à maîtriser. À la fin, je conserve une progression **Niveau 1 → Niveau 4**.

---

## 1. 🏗️ Fondamentaux d'architecture

<div>
<img src="https://images.openai.com/static-rsc-4/vtLEQPgACD4ZHsqNFq3XR-4gaGbwn0gmL8IoqUGekO9QxQ8i3SDX8AlDSIJ9E056PFv4Ct4CbJtHA-Q3zRuCDnXjKOrKK36juUezVTmAEbxXiOw957SZ1ohgzMLIRr11Dp6f4IlYibcDKDBWra416bIQJDfoRUc4gTlNGa2ZHP6Z9kZ7_-1v6dOqWlE4n-HJ?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/lK2iDcQkIcqpLygspsHTi2_DANfTpk1XyNa9PP8nu4ngD3b13E4_ewnoa_lV4jSGVAasdqdkW-_lA3rqwa1-EtG5ninWAsytTmgacSlk5j9xI6XzAnJU8E0vxISQYecUpIY13gTvKP2DSA6zQHKm-inKQdSC6kEjv6nU7aRHyjbaMcae4oRN1YkmrKKpB7Ws?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/nUSTU19BornNQM2yFRvOU1ooIDJa9nujXWsLzCjXWbWD6orUYFwMAI1QgM-0Cdae0j-nU34hFh71tNSFj5z0zZNZjzKUUl78YghsYPDC8F31wzpodNGGdUhYg_B3MUQYjZI7qDhLdQDeSbh4h_vDROUJEUVyLVAH1AokjVZgzJ3CnGfR_tJZGHf6nkj8pARw?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/pBhtSGP4JdYOe31iS9H5KIhTL8Lucg5OF0G0JWAvrt8odjOLntCjjl37B8a6ZjCHe-aGzY0i3EV53Lm2A0oyC09NHXigT7ATb0ZTjBDspVtJbjxSL2xS4nWoM61sLC22ypm5F0FhYBpWJnWxg773bMWIC2un93R7EX27r0sIlGajpdsK8v2AKCByRiALr6Bh?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/JdaUz9sp4ln4xO728rxUJwtUIXlK89FQVm-JZ92qFQEyPYmOKe-wNyevdx4oq9KZd8fLdcBPJeq9dG3qMlDolWvch6Ahl13h_4m23OMDVVDcl26tEhmcnSHJ8Shuyd7EoD7ID_SfXA0D7RIU4PGylvtOxOWgWemPR6u5QwaNhN3hXnyzz3101r4hNutqhKIZ?purpose=fullsize" width="150" />
</div>

### Architectures à connaître

* **Monolithic Architecture**
* **Modular Monolith**
* **Layered Architecture**
* **Clean Architecture**
* **Hexagonal Architecture / Ports & Adapters**
* **Microservices Architecture**
* **Service-Oriented Architecture — SOA**
* **Event-Driven Architecture — EDA**
* **Serverless Architecture**
* **Client-Server Architecture**

🎯 **À comprendre :** il n'existe pas une « meilleure architecture ». Le choix dépend notamment de la taille du système, de l'équipe, des contraintes métier, de la scalabilité et de la complexité opérationnelle.

⭐ **Priorité :** Layered → Clean/Hexagonal → Modular Monolith → Microservices → Event-Driven.

---

## 2. 📈 Scalabilité & Performance

<img src="https://images.openai.com/static-rsc-4/4ofHBoO8pbHYGrmsco7oCT3f1Nrcpff0bQbK53cN-Tp8u_Y_sciNT-hdYljjUro4Z5jfGt5eGZRTiGBaQcxnR8rBC458BXYyInUaK3rWZAlliIlQwlNXUWOQTPBasm8E8rfRVQBUE02tlBztP100Z6d_pSDCC7FeyVEhzlK5D0gWx5vjl4vpfacjyBwQPgdL?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/AQpVdTVzZDSvzW-_o0-uP8mdj6uJPMQ8TMhBEBkr1JhgA8LvctkC7_MIqiyiJFx1CwMoclSjrtA21SZD3PuP3ubRkFund3A45oHsjPxJ0aycUF_i3cq9mKd_houfO8xBmb_a6-rbc8dQuu8zXFIZ9zqOSxqDVnV3VKHgZe4sLfS7UtY6wgt2D-KFUbcIls5c?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/8a_TWUN3v-DgskLGl3mQD7F4dG0uxmSoJXvpFSbg0yqUFR-qxWqb3TVnYqaOfDMlt4Bv7PgymNa0a8_72n7uxa68kHPpM_fYK7c7WvL5ngZmWoBvvkfJK7vtqB8GYBUbMCC3NwIMov4fuX9zYbct9ZpH5syjzTe0USY2viowWJ_wGy3pIQbHza30ttIz6uxm?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/Eom2pR6f6rChHb_U6Eh0rk1kqaZ9qNqkivXYhWXqn-gMCqc6tOStcvzF1gANc9zulGI8cRAFWWyE6Amwapkd0mZKbM_V-rFwMug5qlC75avHc3WH4btvhQMcdX3KoXYSjeYkORZsq4Cx4sE4w_wUiL8SJZZUObA8mVpyMA8xu8tVMfW1B9SpIWCvK0W-Zkfk?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/kyunyExQArZfg1bnYwMrJncvZC3Az61wiaTXjpu-_SeDsJOE8dRllie0dHVYnrgzFWBzH2Lya84PF-3SXE4-pZjDqfp6HEI1Ippl32hrKGBQ9sH6xwpbV5Bvc3pVWBaXrqWNSFgV8RF7P_Zg1DYiIYT-EbQvrP9X9HlSJcz3oEHUC2O3rKwVEfp6Q4SSMhhO?purpose=fullsize" width="150" />

### Concepts

* **Scalability vs Performance**
* **Horizontal Scaling vs Vertical Scaling**
* Scale Up vs Scale Out
* **Stateless vs Stateful**
* **Load Balancing**
* Auto Scaling
* **Caching**
* Distributed Caching
* CDN
* Database Connection Pooling
* Rate Limiting
* Throttling
* Backpressure
* Compression
* Pagination
* Batch Processing
* Parallel Processing

### Métriques essentielles

* Latency
* Throughput
* Availability
* Reliability
* Durability
* Fault Tolerance
* Elasticity

⭐ **Priorité :** Stateless → Horizontal Scaling → Load Balancing → Cache → CDN → Auto Scaling.

---

## 3. 🔄 Communication entre services

<img src="https://images.openai.com/static-rsc-4/upDLzON240nmiar59sG3zmtVOwMCW4ArihVs9V8K8ZFsul5pghCVGt06E9kIU0309OuEkjreB-FwcI3x6s2yQm4YuS0pn2EwDbH7cI_DQSiI_iNoExQlLVFcOCurZbSG7gnGKHVvMXGML7BoqVd0VHZnQ1cfADfWzigNNAt41iF0v847KBUUpjg7GMV-zafx?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/41W6dZikkVsNHhtCybbiyr3JS3S6vAvcnw535DuOgzOhPzNFsVNxyHQnfsi7-PuCh9JKYSVSEpVa4YmTNO577oN-GtnBJsO2NGXS9j_IkA2FzjmZ17t3FEibiR9ky-VPgoeQXwzAPp4tUoAHJ-xw4Xpg9pIXy4AXRxcXErCzKkU55wb0fuvyfstiVdJySlBP?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/vn1DqfeKHt17X_V_dl6JTBNtMrSxFZ8F73GjQGtFDBzhrOLBCsVmlbv_IyjUrQKjpDgA2pILiA-BMsdoBtiSIiZy6XbRL2R9i1xuH7Qgx7fna_veie3bXNbm60j9iU1MrhUnYwXU1YjbpMHLI6R5Be-5nMzcNmwvGAR_8rjRwjDouLV6Lg8WPHAINUfrApOL?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/9zEdrz2TPyb_ZW1z843Csx2QnekB6CUqlDhiPv362Mj3JRsTQ0JTeOLqM6_V4xnOQLoYcb0AffodDcyAL30QU3Rz7nYP-0IIJY4gwwR9rRv_ShfnLlnkTu9lxeKpR39Ecn2jktTpUAeUQ-H0VmHI1ONZecZSeQWrEcKu7vf_MwB5Y2Yr9xWNQFEHk2IYdxeZ?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/tDr2NlHI6cBma8Rn882Hri88iBO2vsdUjBBrrvJmErFuyR6woeVeU7ApvGxhfrKK25dZeIlw-OM0sETi61Ms7u-HbHNx1XtNOkO2xPvHWcWxQhvcQ-ZTbkzeMp1oJ23aPLZWNd5dXpfL70Ki59o5wbYKk_zu4aeVzfmDeOoL5qqa-zXJ8TT8BvkhBEfEHArv?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/nI94fod64bq6YAR4pBcbgwxveB3U4RIrxCtxncmReW5jrNRWo4sl53L-dZXeMX3K3p34RmLhqqm41hTyyDRSs4rqXymUi24xljn5FAHl0aFvj5Fx7yd6laQI5mOgCIuzhdzT1jtWakDATdgiykkxNVj3ZNd4ftLiBvaK713g168SoATSB1mV7eBWIO3YAnbU?purpose=fullsize" width="150" />

### Communication synchrone

* REST
* RPC
* gRPC
* GraphQL

### Communication temps réel

* WebSocket
* Server-Sent Events — SSE

### Communication asynchrone

* **Message Queue**
* **Publish / Subscribe**
* Event Bus
* Message Broker
* Streaming
* Webhooks

### Question fondamentale

**Synchronous vs Asynchronous Communication**

Technologies typiques :

* Kafka
* RabbitMQ
* ActiveMQ
* IBM MQ
* Apache Pulsar

---

## 4. 🚪 API & Edge Architecture

<img src="https://images.openai.com/static-rsc-4/6kc4dPTyxCwWPtylqTHYfnPzxYbSHq6Kfp5fpEIuOx8es3bKjI45wLFr0pj81wxeBre2bm7ZYH2cwEjuvKse8IlEAN2p3XYv7v3j442oYIU0GpMffMohZXAzxoAvyM0Z47vfB8-i7rtkVd0OtMmTuyiI3_EfRdV7H8i5xmU-gT8_YwqHcMvpaZnx2Doyt9Od?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/RvLpDgfbsNZuwNHThfiTBksh07zuIfCUMC5QD2KZejXMk1JYhcmeeGsEk30MdsE1tG8S2ECKNx-wkSSLwIxbGu1KJOQdlxLpUZEbKabe3AlX8ZkoDbX8zMvKg1zZkYOizBAXkXT2aZaP1wsk2022FFU7RfjSpCXUoDPMWXqL5RkD_6o7oaYnPXBGIN2PaVPY?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/uGIUBWITDX_bQcB4YaHut6K1iR8rgqRi5IYkfevRJqHLYLaRaGd9HLmG65ZJpWLcQuu7zScfA7ZdAT42tx6wH-A_l9-q8RRl56mUG7ZIo7FDbwVAvH1S5P9lagMguxNXycIlcUhyjVF_CTdfc8aeGardmXvppxGHDIxw3cZavrO5_A6s6OqhBpW6oiP2EDNP?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/-8U9zD55TVPRx1UDhKqTmiR67YLohaXUs88LIJYTU74Wx0zfy0fGwOBAUbVaxs6YB0OVQb9H_wOktrfZZ1Vl2lmzBLMWzuFNBZcqzGZcJzb92bAPdgnsNy0fDYOUWbISHrvNehu_G-w26ebVKdKkfkv7JwP8IUyqHlqwcTwiClCH5S9iqHb9ytSGguof5yje?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/KhVUTiasJOkx7FQylh78M2c3_U5XsTJYW4RrxwMHc0m_IjRR-LP72VE5SYopxUNUegvB6bDPHEQRsRmc66O6rmfKbMlx-U_F8mC3exRb46NanFfhPWAP5pf4mREZuGA_Bfdmw_DfE_IEq0lsXvXXtPqZrtwwig46uddJvNf2SYHGlWxB-vavw2GpnYaLXF94?purpose=fullsize" width="150" />

### Patterns

* **API Gateway**
* **Backend For Frontend — BFF**
* Reverse Proxy
* Forward Proxy
* API Composition
* API Aggregation
* API Versioning
* Rate Limiting

### Sécurité API

* Authentication
* Authorization
* OAuth 2.0
* OpenID Connect
* JWT
* API Keys
* Service-to-Service Authentication

⭐ Il faut notamment savoir différencier :

**Load Balancer ≠ Reverse Proxy ≠ API Gateway ≠ BFF**

---

# 5. 🛡️ Résilience & Fault Tolerance

<img src="https://images.openai.com/static-rsc-4/wwDVDM7SCFfYzrKp6MaoQTPkX-EwcLCll0pHzNjNAc5gaM7yyU_W931IXNxfyI72JVv4xFBtPuMtbbNSQ1jM7yDZd5R2VfES7N2SH-Yn4K-XWpCeoZ0qul7M1ME6bwv4IV3Oh9AShAbAEbVSo43P1XONZEwgt8d7gC0Ax7Kp-GUpj0hHa8rHpJmXWAndmNzX?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/FbGcOQGa-LWSKiMhe0OOiv2uz39ILUBupqfnLr8pP0J0VEtNiRj8oPsg_m3TsG8KUifZvzULsQtKcr7GMsvh3m_h29k9Q5wEWpz1QV45oiUmZD03UusQNvIiI7XsNQKn2o-DegzYNuKuMCZ0XJM_X7h6shP7zUfHhKyvIZvnpUae3Edu3inlOScENxb_PSpX?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/VvvfTKtL6HizZ743PwcimF9-Qel6OpFi9_uiJcOUHSo7v7u5DeLgdZFdBKbI7ZKGQFXX9OnHj9JIWo3YmlNWHZqqBJWfeLLevYtU5Z4W_0EHVTErxRKCJJzMQozd32QxLUlql1E-_5jHwWYC7NGtAfpmgCXVEGBYgI4rlev00usP9u5i3zPCHLNmBoisUWy7?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/WeaC83d4BFrtzpYse2lzaxs7ZyslsITGXDOn2PQylvFowW6A-SC8f_KsqwoLn6SbsnGGMiQyJBWoHa2JTG8ljU2SNDss1JUfP2-VCsfG2EaQctudCrPYdbBkMuzXd5JkYsot22YrUAuQYANJToDMo-p2fd_EdY2LrhN6qqTCqeM_69h1LG0HjvrVBKZR53xe?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/DOEr1COBI7o2631TYuKlTb7VRXMssUIAoo62Ku7qgUHq3qjz5JcUD626YOWQ4Ff7wWctEzf-ZDVFQb9L3TxlHNORN9DyBKMEBuRXlZqqcXnGm_rqxA6dHE7GIwo6Jow2oVsy-I0968idWKdxr6oebnJEb_v0X9RQMiCya2Wx8DW3ySwhINgpUpLsOy470-4i?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/XBWksod7o6nbNPPzkHOlP46BpnM2Hfk_j_qsTDrJ86C83SrVRUtNNdXlhg1k_A7KGGq2NgsITEOCRZI6H2gbRk_Xl3mKCebPR4q2qlCoZzg5ytyefFQedbm4yd40TMwHvpZ07kccBdl7ZN6E0DmNCFXsr0lv3F_5fZM3KVtV3K-Giog1PIrI9e_cpiaBU_T2?purpose=fullsize" width="150" />

### Patterns essentiels

* **Timeout**
* **Retry**
* **Retry with Exponential Backoff**
* Retry with Jitter
* **Circuit Breaker**
* **Bulkhead Pattern**
* Fail Fast
* Failover
* Graceful Degradation
* Fallback
* Health Checks
* Heartbeat
* Dead Letter Queue — DLQ
* Poison Message Handling

Une combinaison classique :

**Timeout → Retry + Backoff/Jitter → Circuit Breaker → Fallback**

⚠️ Un retry mal conçu peut amplifier une panne en augmentant encore la charge sur un service défaillant.

---

# 6. 🌐 Distributed Systems

<img src="https://images.openai.com/static-rsc-4/fNJ9v5xJA_zmqfcvM9TYOKlR_LzryVbVIiX2xkKGKLc-Ojc1cVXc_lmUPGTWpu8V21_k5I9F9ytFlN7SFKZ5MnhwdKsGftGKH9EGlmx4Ij6REC-_obvfrg_xlAcouUXCOenqtQ4JL78HlvX2hIrmUok0bqk04PgPU--goesxj_IKI6MKAZrAbsUPdwgo-NOg?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/QpPlMGQwDqCOiD9pyDVhvi2gehdPMO2JwlbLXZvB_wCGS-KdBshRurdeaOcgCkPDzRgmVIo_d8UWG4rxFXuaTWWWTuavxBAcBs0cBzjQh65LiMYzYpeZ2y03BYgLSuasEyxEIrIYH7As8XpRxrPNbGFcgRL3GNxgTDcfXy6qpo6KkklE4G4Ziu6kYeFKWkVA?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/c46qAs_NIr6Ko3OCPhCIi2yojekpbiiGcoKcAHDN4rM-t8mLkKWZKOM6cnMy8zMqHyFQJREL3SCLN5PBbNKMOM737INsWL9couy1Pk0HgKiPk4cCa3-s_oNo-aqEUBZUrGemqdzI3jdfeOC6WVF0du4QLZeUP9Fa5XmZYU5MKsZ3Y3iSsvOUCyUGJW8a5ISP?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/GxMU_6arhFcf-iX8aHt8YJScU5dzfA1rIQwzCETKlAMuN56dwA8AiAT7CqDWHvTVfXu3h8MCt1s4QPpWaMJmMahbQnce-gy8BjGlTEQlTY0F8mmgbpq-snEPrEnxlVMie9Xith86Y5t0fI2n8nE2Uzk06hiwlMeAKSV7zympispmzWwQOUeWBnY4wIYcnzvX?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/jmicxyq6z3iwgCywS31jkBkYYt1zChVzLA1Ru-o9LjT7cSThhAqyWBfp7cSJWYzqRRi77fB1-tMUYF2WNegz7n7LGm3I_9KxcxK4GcU7QvThpZxWKDoxIXJHInCLPbbalfTxuNxKGw47WcuZF6kmoyc2bupiHYW9QVWrT1DpDzIo9UIoNHYjeRr_pnBQ6dSq?purpose=fullsize" width="150" />

### Concepts fondamentaux

* Distributed Systems
* **CAP Theorem**
* **Consistency vs Availability**
* Network Partition
* Partial Failure
* Distributed Consensus
* Leader Election
* Quorum
* Replication
* Data Partitioning
* Clock Synchronization
* Distributed Locks
* Split Brain
* Byzantine Faults

### Modèles de cohérence

* Strong Consistency
* **Eventual Consistency**
* Causal Consistency
* Read-Your-Writes Consistency

⭐ C'est l'une des catégories les plus importantes pour passer du développement applicatif au véritable **System Design distribué**.

---

# 7. 🗄️ Gestion des données

<img src="https://images.openai.com/static-rsc-4/O0wfKg72IOoa8HvaPIrqyHu4_PHhOH9Obgt5c25H8EuiNb1Xe5E192a0KXT1szWWoE6DqiWEb3AVqMPJkfTs5AW4xc4UVwuj7ZGCuIBf1wSFev8Z3G4GYUnwXOQLTN8T43z14VgvvitYG3My6sIfbO48OMrmqWdQwy5-Spy1lk5lJ9UjTqdW-D2gkVT9hKL7?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/WAa4u3N_jECQb0pwx9RaeGpTvwKC4S1L66gk8rF2L3ZYrw3DPIN0U0YYNREjgQpUD_oXPghN0lxMn0_MTbo98NhWV1Ra1TU8aLdVS7UTVfzPg6HCzcA3uWDXgLBN4UxfQf49fDGcL4Xul8nHic1F0CZ5bggFEywq5tWISv1RF0zpXu6ipyKjg5VOEtLXtSZq?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/lRrznvnF1KB5nzIHtkE1GmC6Zj3ncPUBou2nWhSIL4eNwQiShsVYg1Z79-v0bOyhFf-F4XavSN7n1cDBY-Q5-NELBz5gi4c4zx4xwVHiTjT1ghHyZ09M6lrhwh-gs9xeOCs7tXClhXtporrMdLIuVRwR4P47FZjxih7NLzv6rMetdEKUzMTE0nLhGzjpHEER?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/hpdU3Eu5w2Gu8msR8dFZCRrH5upRrYq21Q4qRNFlTPTo82rIPEa0mNvjaT-Ko6qagyS7VYk1tiuZpmWFI4pNUSEdk_yTDIzjgKJ0I6bWOmzv1Rll2st32KKCxLALtIiJ55d4ATXVCJo0qhX3vPVir-xxYX14QwyLwESzaHdpTxBo4H0wAGQ0k23BUhA_W1Zg?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/3q7liG0sOfntO5FtFj3p_EEhOXVfm4loQp5AmhmHQtxO5SrEV_bfNMpzdV6zWfkcPqst85GS7-Xt8QcbkbjZSPpuIhXAFgR7orKoq-XKAI23Gb-OcAN1SLDuMOCivrfbM2QAeh0Ds-9oVPvJ-uRhLiYyWDNURHxXWiEBKj7wAb0Sv8Ut9Cn2F4q9-CuZdV4E?purpose=fullsize" width="150" />

### Types de bases

* Relational Database
* Document Database
* Key-Value Store
* Wide-Column Database
* Graph Database
* Time-Series Database
* Search Engine

### Concepts

* **SQL vs NoSQL**
* Database Replication
* Leader/Follower
* Multi-Leader
* **Sharding**
* Partitioning
* **Indexing**
* Denormalization
* Materialized Views
* Read Replicas
* Database Federation
* Database per Service
* Shared Database
* Hot Partition / Hotspot

---

# 8. 💳 Transactions distribuées

<img src="https://images.openai.com/static-rsc-4/IB0aUaIGV_QMLrDs0PmdjBtpQQRVTgqzDsUgoOQdwDFDutH-A_l7pWELZQNLh-hl5tDn5ma2EBR5NkAk-w5WXoNq__F3TxH0XkRu2iwJT-B5E0HlmjKq3KdYsa7PbZgBXU1WGFcNSRqElcwGXDLOld2XiRFN955WaIWs3wv3MkJAq9YRczbB-4lFIhZ9MCI3?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/xmiCtXUG6n5LdCGIyA2cObENXJjXNhmVR_taOZx0wPdXJeSibx7quUgeT9uzkgMbiad6o55tNWxcsmZ7GV4HGZVJTE9mLgo4uNvtr1MQwdE4rR8px_MW_ix5B23wZYbNW5Dw2dxoN-CFB5UXw75jzcva6BNGhB4S5zxX4h4DVaAcRS25wfv-IM2DHO6lScCz?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/SiVfFD77mwaIp4Fa3mZU1HL8Kg8YUzuSfUXHgN-VVPLPI_E1e6el-SGkMGJpuY9JxtAXaMKgp5nR0R76pY4_MuJdnp-rDvebK1FGZF3pMK9KLxwECkUS07mZ1pcg5rwxnKN13EM-BeL2cOUxbt4N8sTbd_zOkhf92DF0hPZyb37xfScqT4kffgMp-Pzl869i?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/22lJH_K2tscI_8Vd18OPR-CVqNZ0MXGa9VDZAwGgawx0Z8JBHcdHovTn2OpdxkQF2X8X-J7gDgrNKlV6jcIj0AEhOt9l9nEOdQPfeKrL9DYL9RRP1T807P84SCvnDOh30UFn5kiq69S4lQP8LSLvxUoZZejC2OBUFGu5xIcmw6niwMlPvot73PQC15F4IAAd?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/YDHEXXWa_aiRMncxDdpKRA7-jm0uZ3MKPNYaGVi2P_Ek_xNAGw8n6_Dg1FG5Hn8S5dCoShpWKsJxYboVVyMRPWr7uoUYmHa6boKny5EsD8TZ_cRj0-7NVeh12Kw8qpcl7zLYsDQthnSD3P_H5Pci0BH2V4lZaLKVqyVtjCRJl7LGaQPp2iUmzBgyKTQ5IpYO?purpose=fullsize" width="150" />
<img src="https://images.openai.com/static-rsc-4/HT6P2m0D9Ber1W-V0qfkM7KRhNVlb6KqC0S0jnt4-Jsllt1wP7GFqS4M7nXTiXd-azlmSXuJPPfnGtQU7S2DjwkGxH0caXGemJnRFBjXmBH9ppy6P1YfvCrt9okpVRUf2ix_4oQosk6I6JePdU06pXq7WYZHjET_cevRLXgItMVKRYNtIIpU7fd7Ud0njSkl?purpose=fullsize" width="150" />

### Concepts

* **ACID**
* BASE
* Distributed Transactions
* Two-Phase Commit — 2PC
* **Saga Pattern**
* Choreography Saga
* Orchestration Saga
* Compensating Transactions
* **Transactional Outbox**
* Inbox Pattern
* **Change Data Capture — CDC**

### Trio essentiel

**Saga + Transactional Outbox + Idempotency**

Exemple typique :

`Commande → Paiement → Stock → Livraison`

Si le paiement réussit mais que le stock échoue, une **Saga** peut déclencher une opération compensatoire.

---

# 9. 📨 Messaging & Event-Driven Architecture

<img src="https://images.openai.com/static-rsc-4/YAh406FwCuDzj3wwBRqvfIheXNNh_QNhNECIkqQ3SkeVYYWGMn_sVINsjVFgkAVF_fSWy6RqUh1-cE5ec4vQzA1JPf_oj4mAXa253bLXKLtx9eGjZGUEhtHoeSdn59w--TdYITEtBtBwHydOgtCAubopOVx3JSM-GtTFBZAbNYoqZSTOW3VdVBX07kUnJD-R?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/i7S7PfL9BX3sk0Nl_b6rdGxaVIKiC7uAsZI6wiZdeWTTuXwOJU-7-4UARNG4Nrw0OGqnqwT4kph8P8jUbB9JeqCbN0AgXZtqupnZe5oQxYp_sgc628kIk1a9yfRxlJndA6-7iN7fBx2jS6LCefmOo_E61NH3WyCg-tKB1VVPRaeeGObUpubI3zR8NyDvBncv?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/kAtpfApVbtuYR_6dOWzwctn9lEbZo21Em_QXZcedAhho5dkmKOiWZl9ymHAvQLkoWxW3-QCQLorfnRwDvFtEI_RVtFeEFM2B1o3cEAFKdKUzJXAIzJxyzpAKgRroyCp_ONltq3v5gnS-iOJMDpzzzJ_LqjLw5-F0WvxHdgqewqYLm70gOF0hpgHUkNHJ_iUo?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/F-YbkZSsvzbDXhEIhwacysPtUFovSYJZ9ZKvkkqgEioFawtZ6GEZAQoAy2nUZz-GEYF1daJbqwvYiJ4CtrMzeQ2dGPf5U3E6CMJmGvWCTD_TrQxZyDxKk6gunonCaIsys7_MIDARly0XH6LdAN99AxflN-HO9vJY2OoLwKxp_LiumGaY4MQS3S_Uy8tP48sc?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/UzIR1aQaqYXaq4RfxPd1hAgH8FocabXEPIvZOHhAIPc-sa_O_O5XCbiYuoF_UgffohE7v1O8cGemI-wAISgIrFILnydq0SZb4pO6IPQl6xoB4k8NjeQbMB_y2JfNSetbvpj2vXkWWaNVDeytxLv09DgOoQGbwcilqEvO4oaFqBPFEKIyRc784SdNgeZvvcTu?purpose=fullsize" width="150" />

### Concepts

* Event
* Command
* Message
* Queue
* Topic
* Producer
* Consumer
* Consumer Group
* Message Ordering
* Message Deduplication
* Dead Letter Queue

### Garanties

* At-most-once
* **At-least-once**
* Exactly-once semantics

### Patterns

* Publish / Subscribe
* Competing Consumers
* Request / Reply
* Transactional Outbox
* Event Notification
* Event-Carried State Transfer

---

# 10. ⚡ CQRS & Event Sourcing

<img src="https://images.openai.com/static-rsc-4/RauAXFiNnAgtyGQI1fWpxIkZpfCJXxSuP4fq_BtnS70Piz8V_hEnLOMx0-bk123f226ydCpvUHz_nWufuW_TmP-BhLoMT15DdTxtYDSnpzUMTEAPu9I4T5E9k8Oz6Aw_aclB9EtvkyNPsJ1BiOAt-UeCWZNAZGr864b3rRzAfJrpB4dd3viNoclOYhWBqJsw?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/I1j2EjS-fzDRoPE-ji9iXuUQQ8RF-H6zzS-6-W0aJr7M--amQtysxr1_lf559CLI83bWKqo_5JMC8oN4Fpyu3_sIQU9D4zeUeK72lUpv18hEHFA3TS9SAYSHOxr7kbs53hlZF9Xr3vk0RDaQApD86XA4OMq3MxGMg88y4YmLugrSbzSEc8L6P1nwXo6lMkqH?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/gc6mngmuzc3jJ89m9AHHAGowjiGFy1-isiANcXNl-6MHV-qKeqWoP4P8sY-CquZL9P1XVhSe7w21T-ef5cQjrU8npE3pNWH0DQ4S7BLaT8RGKtktS7xccjdS_x7yiPyVlVOxTSTdcOoriA6Ls8z41WfrILTX9jvnI-ruKnls0vbQ8pj20gn3EOowItSQA0gI?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/eSEUt9eG1Yuuu1QF_je-JeZc-VEY49NxazXCr4-vVdCyOelvx8g2PzgJWlwY3mcpN20K3ZwJHd45vKUwhzjW3SQ2qiwIZFj_kYPguSWBf5_HiErCKirtEbURyTu2VuHb1UMrCM5_TEDQxp2NIyyGTDRKVSMyXfo99kvCt9wnVhw-GiTWIyZtPsvbT_V0FvNZ?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/iKrU1lMxXf0xCX0fb7C_r1QCCg27_yfAQu8q6auPsKwZHJg0pAZBQRPqTlRDb5u0XDzUAZHfjcOd6liQ3SIkO4wsOPBjQLYZ5wo2Lk-6uFoGvkZmKT2uIwtUP2JGCUkNqWsT50HwYH70Elol1FDB703fSz_X0P949AzMpUI38GNTI-Y-YWyMkTtMJuznC7bf?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/Z40s57_JPamnWIDcOtBppEdfwTJmPrKiJxSuiC2kXwQXn46AnDXt2lVQFmkqBPKnapsA2RfkCSE38Mw2vOme01bIF5WP5bB4DnLophXrBb3J2C9mkTFGHNicR63gCz4dQT3mZ2pha4xlHQHq3T309hxIxj0bekzZ_37dhcOIfNnVspxbsX-kH92tCDVSCyLL?purpose=fullsize" width="150" />

### CQRS

**Command Query Responsibility Segregation**

Séparation entre :

* Commands
* Queries
* Write Model
* Read Model

### Event Sourcing

* Event Store
* Events
* Projections
* Materialized Views
* Snapshotting

⚠️ Point important :

**CQRS ≠ Event Sourcing**

On peut utiliser CQRS sans Event Sourcing et inversement.

---

# 11. 🔁 Idempotency & traitement des requêtes

<img src="https://images.openai.com/static-rsc-4/HJK7QzqVCTgaBGaGeWVdlHJwbj67pBZ3wGfy5oR9LV7soboFwPPqbo4K8LEx75j3YsQdGW6v335rBn1ZQu6ogGxbmeh37vP4M7qXqM6u_vgAGOx1YhkWa3PO7eWu3dn8lt-bgSLLiof5NX7TVkZvRcywKB6moq63G3Y0uSau0mQGxJW2nZL9lBNUPK4ahFsU?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/Z-nJJGsOIQ9ZDyEtBSF9w6TMq3Hw-YnbQ7z4I73WUvpc3haxhd_WS9YBPEn1SYrYwHLJ8vM_BpHffX5oWHNBCF7Hyq_SFcpdnGbGcTUXfejdJcDlaQlA_KlLxZ0fCnIY1FBiHKWPBMUNQ3Z9BjpcuZmOoXQ6o0is33qBnl3eliNpJb0tFxJC-r7GXtGsrzKr?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/V55XHZubu_tQuhC1d1WVb_rzPL0aXJbjtJErWYhUvRsOWdXttwEnWwE9u4Prz102lpGljte0W1uUZiwhnqurqY94_-IByp7KtfKHohgOmWbRa7Dv7xfRZfq1fWsZYJjYTZolQ7ihRWLVf9bfx5Pwi-P4xjt46N0ZT3FltJZbnv4fUTSAlNWNMOFN3UQg2cpK?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/LuPaIElDkNHFBDnpRJRd1c-jcieMLyeM6eD4MCHmFcd3eFeSqmVdYukmlZFnmLbTL4a5MSwNRi37BgMbjNm00ibBP2LUzE3LUulH8uv7YCEUVTSSFbOqfulqsAT8lugFov2hr069kp85n3KoLmfpkXoA3fQhElmLeuidEvHTkWXgUvKkikuKZJr4ZJu9EOIf?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/1Q6y1XRUUhiL78HphvSjAsMY-J5IEEzpU5zFkEl4_XOeUgyCP5guxvg_4hw12WeG9IlABJdl0cg41bWT-MYxIULLS9rWTV2h7QkFbWgcumGM_vr03hN8Nkdl3ou1aGTzZ80epfmH9cGFOG_bhGcXg-yGMnQPr0JXAUYLOVam8TitoCoa332P-JthpbaVWWAq?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/oFQ9w9jgxZLdrQp-3RttvngCEGfwi71WzqhB7-exKRRF5UJheKsRiQxwtoULJaVhCN0Pk9RtDQcCm9gVhD5p_QHOMLIwK-pI-xaufX3MTqwkGEbsrz55wyzsr8LFTWRw_TMC5WeTOnr2e3IIF14U8OhRDGJyNHslStiBJzpImIZ5iU2IxIapNXxDyUKmpQi-?purpose=fullsize" width="150" />

### Concepts

* **Idempotency**
* Idempotency Key
* Deduplication
* Request Retry
* Duplicate Request Handling
* Optimistic Locking
* Pessimistic Locking
* At-Least-Once Delivery

### Très important pour

* Paiements
* Commandes
* APIs
* Messaging
* Création de ressources

Exemple :

`POST /payments + Idempotency-Key`

Deux requêtes identiques ne doivent pas provoquer **deux paiements**.

---

# 12. 🧩 Patterns Microservices

<img src="https://images.openai.com/static-rsc-4/JtvqA3qYW4u9kGRXPuUzo6wPSWabVuL-LneEHwp49wc3yWDtc4Nd7eRjlPUorkmDGrLM1rlIU0KLfBeotf6oUlGXFrZj6AN6vpz1EYplVrGA-NNpUWFULovJ1aiavvTcEqdaQ9kY0XrqKPETwI6MDZLFDrsW_knwTV36hG97EBMqiRqpQ3dr9xlnuGymCExS?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/hR3YAD9Hhiw9ugbp3DQjRtb9CF_52e6NptDmj5NxvhHNK2uS0gs86c7co1bFct_rlM0O4TXbPahvYGVRepTGwt_2XJNdBIULvTlKFvUqTHbtYCpDpMgqdS0tZ5xPJYx0EFiCFJD8AVVbVIGaq9cUycnjCFpsX7DT4JSCOYyzXTvhvbaximdky4f_VH_kFLLi?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/BV-2iuXq34eOkGUX7GzT3VfN9eAe2U-PNqsX67poIiNd6e4BbNZYgJFFxU2DDlhzLSjgqK8uLrUgEKG1CffO4HWYAf3X_5k10fRKCJBeYd5bl2zYJ-DVQNB6Qb7d44eJGqeECx9c8RIWDecz-BtTL-nuzj_TPk1ZaZEDvHu6MJ7r0U5E9BowAhLDbyNzdmEl?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/PlKmDb3xYKUqGD1sTo6-gaWl44eLWGUPGw6SHijAmEDGwaoEwlOS_d4jkwVwwdryTbP_4geUV9freb38ahNPQ6wg8z1d8DyRZqKYPgxLN4V0-qGW9CP2iZfqC5btfm9gcNWGXMqbGMSAdChnSe76A95LzI9PWQk96CLW2MyHnKLwTEXYA9kgAl-balenv7yS?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/D8qFcb30c4ZM1b52adeAijyng9Fn8BuIPkTJbrTPBrnsI8g1tw2pqa5qSmYKSm1VghShD-kWtYNW1MfqgqaUJgeStAW4zrg_ciXIeUlDEGzU38KCz-ciDVl1SyeEPz2sw6j1Q5Iq9N5Ec_ApW9THcOm5RYu-t4bGCajRbQZmBVoFe2yZ3Js4T3cRiBik8WDE?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/gXTuwtqCIVrKDXm878Z7ry63rWSARCRG1-lPHRjq9WOe_4FTbnLnNpFWMP1zJX4CDS69xtLdzM2vQYbQxjwr6VOhS9nSFW4-N8H9QzV6XrDtEflly2tvzmUtABQuknAyHBvzls35YHq_EKcmhI6Vd-2UH9tYa-IHcSydp0_KmiIGrdp2JadMybG9_V0u2UpP?purpose=fullsize" width="150" />

### Patterns incontournables

* API Gateway
* BFF
* Service Discovery
* Service Registry
* Service Mesh
* Database per Service
* Saga
* CQRS
* Event Sourcing
* Transactional Outbox
* Sidecar Pattern
* Ambassador Pattern
* Adapter Pattern
* **Strangler Fig Pattern**
* Anti-Corruption Layer
* Bulkhead
* Circuit Breaker

⭐ Ne cherche pas à utiliser tous ces patterns dans un même projet. Chacun répond à un problème spécifique.

---

# 13. 🔍 Service Discovery & Networking

<img src="https://images.openai.com/static-rsc-4/ZLkp4AT4GPydbZe__BOV4mBRAfdsLqFZdGKDoRcPYXiIaJDv-L9XXObws8Qho0bduAfuapYvUALOyo7yW2K0YVp4PqSCDboFoxDes78F1cRPqcqy1yE4dQVkkjpM3pkm8XY1TeI_8HGbno9i-8qFv4SmyF7ihPti-NFMDMA5bU4GPd3UaQmaTseZ-0ziL5W3?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/iEc3yxRDp1f3UdOLALsG_vs95niBSgWLv80-92EyZlSbJ8l08isni6aTEMhGNQdNrv9sVlgSV30PsQxk9OajN1a9t4EE6SCPbneO1aLK3BjLUSsRjzeDgvJ_bcvUxCSCeenxw2hkZPBwkJRI5s14WuMYkO6UAW7KRcI35rGgaDTw6RHMOX5AqjIlccotlENg?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/Mk_PZZJJPsLl1JRxzjXErYlkKyH63Ce1QIbV2cPNgN_USyiF2Dr5ODeR5VLRT41rO374c3b_N5p9Okt0UoGRRRp_YiQA1I9KNqCGp00EA37yllJwsRR6zXRHPsBHAxQDqnwoHQqQmeSe30qAlm-fk4fQdFeMm4yCx7G3MvZamTQGZxIHjYNAxGcVF5n1cyte?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/0UheaRVxh9surGM_snaEezs0nP6kBYp1R93fEm-SwaGB591pEyb3b4_ofRybDltOF3y4hrhtG60nv06yQzUNJRsoVYZOVu5T6NlnHE7ZBfLzQDkFWcJngMD0zygPdNwpQAKPiBmhzNNh2JegmD1g_gwpK8AxpiECWj2znZJGo5Y8i4Sl3wGao7OWkVRG_2Tg?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/yN9YfNCkzV6CCbtz8STWX-tqy2Ua2a13cNX_zPtGwQZ5bPassypKj8fwUFKWjzSFlZ4OlhL6pnLACvzaP3ubc1lgJharkiiUIIYT0ow9kJAOza6hM0Nsz3eya0r4alSAjoBIEX8gJHc4thpogTQZjCnvVyp822d69xoyPl6Jw-wQTeilwDHhlYKHqVMagk14?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/-TOeY1OJAfemc53zgCrXCTcWDmomhQS7ZLGBaFEG3_PR2vLOYxIvNdKzX2I_PVacJjLJmUO9OOAyrRxxFKTtmXyebL9Va_mnhy_sAryRJwZMTn2XScL-cNavkbYs_zikWplbJDp6cEPRaarbFrKcjo1PIBatLvszBjievZOVyDUdoO77Ema-MolhyMFtdI5f?purpose=fullsize" width="150" />

### Concepts

* DNS
* **Service Discovery**
* Client-Side Discovery
* Server-Side Discovery
* Service Registry
* Load Balancer
* Reverse Proxy
* API Gateway

### Technologies

* Kubernetes Services
* Consul
* Nginx
* HAProxy
* Envoy

---

# 14. 🕸️ Service Mesh

<img src="https://images.openai.com/static-rsc-4/wTUSifq15eRKeNocH8hzLfP80EJxcuNNb4Y3Riwah5AyF2oBGj7PU2c9W_DdWmXcjov_5EoMlGQPMFuXIecyKsAlTkp_lT2igwYqeHKiQLtKOlIWic7EwhkFiJ_h2Sdpaq4DuBZC1lEMlXpGdA3P7vCCOoyJxI26hHrwGShHIPZwIKc5RJqi4oNM_-tlEAkO?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/1MxWmSld7O5mReZjyxHmdgrO493yPp-TuGv3CV0pyY27jaOHWoaA-6kKeypmHK3BCZQkUbCnJW8z95P0_c9EOpojymMunzXdhmErXN4MGVJA7oQszYGmVCDVrV1bu1FeDpE23EsRVe4UjVL4FxWKVGa37JIal8S20doQHOw5c8nfQ0MoGzR0gJnFY9vCm6yk?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/eixVL7g1fFo5R_gNHtTBtTSotrAqD72H7G-31eLMbS_89L8-foo_C0zkp-gDtuBag0Ft8WdHTwxCeE4JKJ_xbwjKNMClHWZMteB8EbleQJ24r4fI4fUo-19bpml7aqUcVWxz1_YzJcL9pPYtJXvkOdJmGkWIKPHs4Q0gYjpAaWpXd9NssVJYQ5U2eW-rW5qV?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/gVYh1tev3tyl8tESDuw1MiSaKU-QPD_7WQozc2tWyqnuUrr-rHe1VQKnfgvir4LbgGT2i2DUY6R1PHYUfOQYc1Az-qwOcNZRBRom6h7_fGV0aUR8s_78yb5AZ2tmULGlF3I6_EfDjB-yLNQUrygxBxQE6Nn5rP218MRKcFxKEfcyKU1uplbjJqJJotRuzFCM?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/YYUDfVsMvYdMu9uX2JucF6fGQyjXIj6TzIdsEzsEhFYf7fSi8Y0W42Apbyn_Xs1aoBWcB1LCmIAe_XVe13Z9aB7_fcYQgfYhJknaqid2b38ufZKHDFJVK5hs5R_pTlGKaTMrlTMe0wq62xqrLY58N7fZxr44dduVB5JOOVmDcCH9vgzdscTNOqlfT1JClamj?purpose=fullsize" width="150" />

### Concepts

* **Service Mesh**
* Sidecar Proxy
* Data Plane
* Control Plane
* mTLS
* Traffic Management
* Service Discovery
* Retry
* Circuit Breaking
* Observability

### Technologies

* Istio
* Linkerd
* Consul
* Envoy

⭐ Question à maîtriser :

**API Gateway vs Service Mesh**

Le premier gère principalement le trafic **North-South**, tandis que le second intervient principalement dans les communications **East-West** entre services.

---

# 15. ⚡ Caching

<img src="https://images.openai.com/static-rsc-4/Uc3H1AwYn-L1q0UzW4lfLv3CJU460yJXZqJEiI0b-ZXhgMlnFV5hZkMXo5qfUHTDnDyNSNayRk993h5I7sEqEFCjEt-iaDDrwxwvJRC7wqPlf9HpVE34-y7CxOsjQbdvMFRFFrz9Nz0DDEb45rh3otlK9k5m_xENgiqYGaoxhd4kdwiStC3aOh_pVwOEbDio?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/IlP-W3zLbGRs93xoNh_KUWOKRsjGKgWGlVcsjUOVYuROh0HqI3t2oyX-oFxL0Cw4kUUNkbi_qVnJ8KaYYWoBnwc8IfolAlAOVWdNwPJ-5HagYDfC5TlDvTprYs_RZb_3fd8b1lRMkZkzAojJ9ymc6VOP4c074z1bAQs8uVkuAUH0KM7dMIx2ILpF_HRikUxB?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/IVyFoJhnbLCPM1sjX01MTgzS-8iZm0WfeCFV1M7c1ocWVjZWaej-ZtYJsRYqHjlyn4a-phcNGzr0o91v4Whp_XwEDWwcKomTgDQk3X_I6OwydxlcslkZ4M7EeEzbSy-v-OkyZJkgTfTSHXZ4-WCIom8akMOR5YikOzLY_n-YxDAUjEbbcZP8TcNIz1FPjZmy?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/SsEInwOwH2_SZk7XbVsVDRs1gTzRo511Ufc1gVVXEQVTMcjm1uXtfD7i1etF6Xql0fwNPw9VRsFtW_6gS6MENrRktSjXjqCjXh_97koMKZ0DvhTdl6C_xj3SOaH38KbWEghpmkOqXZMSgd5HR1bF4tKoHQyEC7GTEjD7WSw94s1QOVoZJdCdrEuJK3T1of3W?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/-Nc9qyaPI8LeQnNB-YAsj9ql4ZHyX4ofcS7AMgfEnfPuLoHBPX6WJJO5o6xtIDu0eLoloBquUm-Thc58mvZ6N5rlrfmmWNlGrwovPK3mDx-f3aEyFiWYFhI8sHQfMCN9NYt4c8r7JpPNSzH58VtwNJU9mfBIO3UJSkMxfQDmsVs_bSv1aBo0uj85cDA5UY2m?purpose=fullsize" width="150" />

### Patterns

* **Cache Aside**
* Read Through
* Write Through
* Write Behind
* Distributed Cache
* Local Cache

### Problèmes

* Cache Invalidation
* Cache Stampede
* Cache Penetration
* Stale Data
* Cache Warming

### Eviction

* LRU
* LFU
* FIFO
* TTL

### Technologies

* Redis
* Memcached

---

# 16. ⚖️ Load Balancing

<img src="https://images.openai.com/static-rsc-4/rFTNpYvPbunfwbZiibAEHq9_pwIIw20alR6viTbufdjx5X0CFZNgJ0anVvcspsEYwX1uItcnHfwC41MJBCzsGU1HYnygrA_fwnMvzZiUX3LPlf7t67aNONRgCH-dJi3O7yEv2C3PvMIIoqtNfyPb1Bh9o6-sQPJnM4oCJIR1ZLqd3meRAvPES-SVLLUeibJh?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/q7DbVWCmo0uDsMR56EpSkqSB-us1N87QllECAlhIWJoYZq1sve4a3qqBE8FsrFK-ZrNax_XYJODfgdss85LOCYWLKkjkDlHM6qTf_tQWzUZAMMsCkMuICkekDY2z0KScQC9xdUj_bLl34Z-4Tcsbz4LFeYKey2iG-bbqhpqR6imj4R2jLzDdJH2RLlCGEnnu?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/0xH550KI4VpA_Z6MprXTw2a8a5Ju8gQILt2h6BpnrYBbOBaPY1uQDxAMVESQSDSWkL_OFiTkvMKnFL4FrPl5ZrYIr6a5ugHozou-I7WefolSGHeVOmy5aCokrdY4SQTBpGNtchxe32FxOPnQKWlSrDnr5jBcNwZFrInvjYQ9PYFXO6nS2cJySrTQ4oyfwOAh?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/yK2yg2o2XsiFAWuuordVOSxcBkh41tTQIh1Z3wN-PnmojSZt5dBOczFGslHWlb20eNbrwnSnedY9G6F9pa4pDE2s-1Ec4ZYgYBx4FjNJoGk5FRBxDN9qq8dSn3303b3YCgXrYECFB7awyfD9KhkpudrKnH6gckQ2fSvF8SXmmFfB3mFGY0bWkvcn5ZO6CSUr?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/9diL65E2qseR4wK0uQLA6O2gkWdFezFgvLTqu8F3U_6HAmUDs8vY8rKuoapJkQc-26NNeeg-2BDq-SQlOsNhlQfKbS3r4Mjq4UsIHZNT6wHyxFRT4b67qs7ujqJhbWjLq2-FMQGtDy59dAsfxrJgshl6MH4aHXu2sR7O7CDwVSEf2o5gbb625KgS1_a_DmT1?purpose=fullsize" width="150" />

### Types

* Client-Side Load Balancing
* Server-Side Load Balancing
* Layer 4
* Layer 7

### Algorithmes

* Round Robin
* Weighted Round Robin
* Least Connections
* Least Response Time
* IP Hash
* **Consistent Hashing**

⭐ Comprendre pourquoi le Load Balancer est essentiel au **horizontal scaling**.

---

# 17. 🧱 Partitionnement & Sharding

<img src="https://images.openai.com/static-rsc-4/X0emGQzgznTn8hpIeKo514S8KrZen4OnUDKWXVw7n8KKxEcO7MzKKbY8r_F9E7bobk4fDjGaDVQB1GSAjteGW2u-2kPVqIsl9L4iz3GsnVbuOxCtIJ04PITi_gDASmvw0mDe7MxBnvr5LDxgdfpkCFBKtBV398furgFlfXyEzkK3nblwG2dwZEVPjH8LyebR?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/N7WstKX4_bmNxsx2Z_c8fblGRH1r5baAnz6NM7D3pVDB4iCdu4p3mlOn75tOmfnV3ySw9Id47ybaKXRbtm272bqsAdD90A_npCpYd_sjn5ZZHPmoP12n-6p7XUQLtZyBGATfnSdxLqSbGc25MDXYJj9oOUesFPBhT0lpfjBWluaeFMniulJuQ26fnQZlYIhq?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/z27In-d35M20H8Xl6zovs-ka5UprlBE-BMjpiAUeyGL0NELnXkQqA5ObWiG9mSiHrt7FHJQvlGq7SKMqAfjbm0YJTEVkkCk-N66DdrdXF7rAvrA1mEjPO64byDy6Sre27Ey6IMMGssanHV8Lc6NLfEUzybQY7NMJ5ctHsKa2hfF8FGuvZozWwWMaBwEwU__r?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/hpdU3Eu5w2Gu8msR8dFZCRrH5upRrYq21Q4qRNFlTPTo82rIPEa0mNvjaT-Ko6qagyS7VYk1tiuZpmWFI4pNUSEdk_yTDIzjgKJ0I6bWOmzv1Rll2st32KKCxLALtIiJ55d4ATXVCJo0qhX3vPVir-xxYX14QwyLwESzaHdpTxBo4H0wAGQ0k23BUhA_W1Zg?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/Th_tKYbh9vtzoI7ORWUi5OwI5raDVQV7fKP98oqJe4Nra9rhSYvsmfGQO9u7H5d1aAjDoclL8fIVwZGPQhomItnycmKp68eFQIx2SMNSinxaABKW-s2FMV-aTPhzRigjHGSNVVGjLYQSsW4kMKf9VSMEPinQCo5Gc0UtWpW7--OLvUHsAN1x4GphDCKI5Xbk?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/ofo7EYHH4VadSY07MbrBMyUlSvDgn5xELd2CLiPYi07THtMuAvJ8l35efSNO-PrN6hmLRQoTLaf2wL9k3TVzRMtUDERjovtVDMYci33N32DQi47yrn0QTDwt6e-0JHFjdtlgZI_ug_C63xPpCqCPWQG6wvLrvgl6fx67FbIdeo0PMTFW_8wpxc2rMaabMnRM?purpose=fullsize" width="150" />

### Concepts

* Horizontal Partitioning
* Vertical Partitioning
* **Sharding**
* Range-Based Sharding
* Hash-Based Sharding
* Directory-Based Sharding
* Consistent Hashing
* Rebalancing
* Hot Shards

Exemple :

```text
Users

A-F → Shard 1
G-M → Shard 2
N-S → Shard 3
T-Z → Shard 4
```

---

# 18. 🟢 Disponibilité & Haute disponibilité

<img src="https://images.openai.com/static-rsc-4/HckHRl66KEZHcrdZI_GDgl-sWP8mjck2xQSaNzIAmnOYFPH1kRinV2Q8ro-_lKeei3RvV_EaGu6Aj5h-SOjaBNwedWOlpeXCGyDrhijnG71TR_mMwnq-qs_KG76nDq5ymWIwh7FEMA9pZYLpEBUygxyr6GgDOEbsb2VrAA1RK9vXPvifFdARSfMegAla1ZIS?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/qWs687mitgvDpLehTnmZFJio-_EmcVsWW975L02l_uzOw9BQ9UI6FjBr_IjIUGN1PmKQobkAg6h3EbFm0Oa35xEZ0fy4eCOgTkhPtLhlABy3-ylubKA3zOILwLvDTQp8DvM5eDD4LAZ7FYxdRkBeqJiXY7ZkTyVMnNI7jbJGw7wHPsMMgEdhqsjLbSAqWoLx?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/cPOdaZLvWdw-ZZa68j97MVCgCdn8gS7dfNtf-U96QXxu0rYFOKn877LUvh1MYhDUnzLPOA7i0oUogHf9hzp-VMpIZf41b9Q4LxjaHGKCgbGc5ztNOb57GlsTIb1uPIgm5OLtwkVBS-TqnzUFXEqrtlh7C4jX6vpFHLeWxy_j4t6bwC6mWKnIHAillIuZ2IUl?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/yvNuOIRrKQVyNB3DJMS3OEu-Y7Cp0j7xYm1oBNN0PyWcSCklszjPknuTgHjw2JP0r7nzATNZ4IVbpxadh7EnfJtlmWeX9yEGyd-XTNOh0MOMYNvfhEBi5ShJRQ7GuTLTPaiKlay7Z05Mq7YWBBYxRZB7mLTy70v3sDxuSmgN2nuVNvSO1q709sCv7xVODtI8?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/0eYSuHlWOqXO0M0SKppnl31TyVF0X9e271KUd1pdTUSVAuL53QN3iOR0K3roCR6f22yVvuxDk5ATfucZVUc1oeJoC3Z_jd2DuSdP7f4wDGj0e2yhL4_Mo8fBdvH3jjNYqRTHrSl82QHM091bKLkDwUHP04yLKWGC6veoUk2j2SdwLsKK5XfEef1lVptOpRlv?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/YmSUzsZP_BYJTYlv3YKPJT9uE2_jLIH1Fop8fUJIx7BQAz9rKsp0CmCueUw22sxiELOAJLeWFj9Un7akwQYJyM6TGiLgdScXz2BJC0pe5AcP59j31fyPrJKMoLPqx_7K3h9m-CedIDQM_czSnGgdxUiJ_vODsMB63HA5tMbKHmScMMG2qYZu5xF-hrxPw0d1?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/-uQEK4wqMyqm_ZqEwc2HCXLH6DVQIb929iqO9FdOodTD6FQZ7Lvzev35plpytWvYaLusF5bo6QdFitoneIS6vin-qURk1N6QIj9pZLykkzZbef6sKD1JOiputs4UIYVaSjRTEH43EDvmLi4kAv-O6xlbm2IPUf1ZMG7O0T401wY8yEZLxpD04VLhHG9vy2gO?purpose=fullsize" width="150" />

### Concepts

* **High Availability**
* Fault Tolerance
* Redundancy
* Replication
* Failover
* Active / Passive
* Active / Active
* Multi-Zone
* Multi-Region
* Disaster Recovery

### Métriques

* **SLA**
* **SLO**
* **SLI**
* **RTO**
* **RPO**

---

# 19. 🔐 Sécurité

<img src="https://images.openai.com/static-rsc-4/csInGhQkK9-2vo_GFUK40wvfWOH-JJ3zn--nfgHDnmBJWv672HVAnPEXHlikHXQrMvqhlGC-1NO775cq43ZJOClHutapPgvbm0ieU5ZOP3AvePX7roj7kSdgdC-C4VfEC9XHjNwiODvV8YDnhY5X8nPbPps4tBg9l3_387uTAGyGDkCAmmtQOiMVUbXaoMmJ?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/gbwuWQJ1meYu9lNijfZBQZN1Bza5Sf0a9HulY2INdceaF_1lBluXOHWwgb7_fiASZ9jK1dKrKUJymRyigp2P0XnledHbvVu7nnPCHe_KB-_Xeuu3Jp_W1DNKszVdczJag792bifGUyaAZNkJK1ZX7DCjv1Ib-RTRyKEgl9qz53GqFm7iTSdxeauGsNID8VqB?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/PpDbMcks90YiwlrmDBANwu6oBpogHGsIy4yWip3ADra6g0fV8gCJZO3I5crKLUArRMZFdMJ3j1riLH9PCFe-z6JMKVw3WoDt5W10Umz_imx1TwRyqVUTw5LBgTOINmOEN9NCHcfLbXCRGYQm7X8oc6V2pyQsQtgST92M_2SReglNrErT_XyJuz57gJ9Iyv2I?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/9RdnT59-zvL9iAQNuKVcEAjWYy7cr39haZm_ksu4KjYecAmjJYNK7G85maweAgr5Gn8f_VYNQEmiK4lSJwCJ3Mf8yoDXlKbFkvbijk7PcmznXxPUG1d4UdVJEwntstrwMe3ONyuXqvN3qs-ahMp2xQ2iRIqYaX4fXy_FHa0DXSsyD-bnTP1oaAE6VNw4pOzv?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/5PmMMsxwcqqOeAboY7fHw_FY386y6zrjCzGXghunKl9P_M-4fhPJegoCu_rQRp5B46bZ4L5qMIuwIzWeFHBUy2l8rNtLjf2UI1lRx3vsnlZASJiAxjprrqi4_RErl22VbR9ctNIMxMrayRKhaFfbaBMCKQEkvBd8uColp9pdCRvEXrPfq0sf-Mhu8YJbvs2N?purpose=fullsize" width="150" />

### Identity

* Authentication
* Authorization
* OAuth 2.0
* OpenID Connect
* JWT
* SSO

### Permissions

* RBAC
* ABAC
* Principle of Least Privilege

### Infrastructure

* Zero Trust
* mTLS
* TLS
* Encryption at Rest
* Encryption in Transit
* Secrets Management
* API Security
* WAF

---

# 20. 📊 Observabilité

<img src="https://images.openai.com/static-rsc-4/kz8RZypSP03N2UnObbos1q8me9QV1JXoTZLNLUFR9O2hOJ7dFDroVlPbzdJKY8MCOWhmpfH9p2Y1u80ITfO7mFJ54QDM-X_aqWEts-pwThmXx8MHHcBZbpKqWzNBKzlDm5Za4eQh57-vPELAIosNS8GHE2evm7sEhSCRm0JtuJrvH4TCxt8CJwOriahojCY1?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/KHqpR78AZh-pLKDG6Se_MCAt7vIyWNk-5JTn2T2HIDydPQrXOqO7x9D5jpz70l0EgahYQwxmL8f0MQu3qLlJK990hDxZ4Ty_cw6f5IZVqJfi7NKXEeZOOecKPYQCENY9sh7s14bOIYO7Ug6BMFSQL-ON0edES0zfZpADvZolwuKFNRZdYrSI0EGCqCcjNSjs?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/iOLDCcG16p1RLg8PmuD077KeUiuIK4A7geb6yXfRCygYJgdx87D5HF1HvRb0gw3jJa7UecPGQdXTb_ixjiSrI6OtbEBSLeRlB5y8NjXnO63pu-ElBHeapHZSbPBnuXadDHDu0tfp32x6n9uCD2Mcy4VanZRSxTPFYktlNSRKLMkoscmDBd9PEfDYkjdWBR8a?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/wD7FYCOBxEMfVYRmrsO9Ti3buHrbAuc35qJBK7ozdAzqYqECPTN41LGDsXhhhFM55Mntnd7-ZdXuq5lmyTJUIrDeoZ0jbGgSwMg9saVBf5HM6ffADYZecPQ90NS6NlbyCdmwJRunCbJCurcRUYOYGJqtdbzPraka4Z1rl2HM7OPrn39cMb3dbqbXfGuG9WZJ?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/xSsXztYSk-ujoHP25BypEuZkg72A3SIVrPsIVoqkPp_TEqs4Z7O9dxIddfWdVT-iwQlEcWgKjLpoawNvMzjTLCfodAX4ZPcUTam6USXXaRUD9wLeNXPZtppriVFSwU21vXvCCLebrmjTLdI7dG2zcjPIFUXwK9XJokeoYb_9914O4RGKOezHmERI_-w7SODc?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/4UqUVnTgrxq2L0HL9o3PhXpDQN0dB57td59WJAZn6jpIVae_V-r0kQAy7W7lFLR3PFYZFaPqkf7zOqSoqdkXqf37qyGWkE6z-nnYaHTxDsASX6PKwnm37Zs-c-ezjfjl0yrUK07kmTrH9XQObtX9iNFrd721Kwo_xOJqvrQ7jupBkB7A9IZDEcpvXL5sNTGS?purpose=fullsize" width="150" />

### Les trois piliers

**Logs + Metrics + Traces**

### Concepts

* Distributed Tracing
* Correlation ID
* Trace ID
* Structured Logging
* Health Checks
* Application Metrics
* Alerting
* Dashboards

### Technologies

* OpenTelemetry
* Prometheus
* Grafana
* Loki
* Elasticsearch
* Jaeger
* Zipkin

---

# 21. ☁️ Architecture Cloud

<img src="https://images.openai.com/static-rsc-4/2RDtPKvAvBxouZvIAaiONR7b-b-XxDoZRO0Kgz71cdfDoPfBlaHLZKhdVOd9ERMpHqnCN0llBMyK_ns_lCgs0fUGSJL0Ao1s0Q2EkPAeoAADkqGdjVi-CyWMeiXRmGd5QPv1v9KfVy7kn5FIzPt2VP0p7twb0oicaFmuk-pZlGb1mGEzmAmyANif7p2V7of0?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/Yj7NdO5jkKO1YmKK-fWX0iFF3oc-4COfaOwzUT-us2myvFEwy9n6-2dg_WsrelstK_4HmulFCZ-h8OqQ-7q_Z4O-qjSHHhpJ5ah_xBMSP1s4VVBjnxFhTAlCpmtdffDQWbXhU2E45swkTtqaz9fe4gkpBLstytWmgTg6BlcL7racYfKLW2tx4kjIdLPhaj9r?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/9Cm2OZ6fIDXadYu5pQ9a054QmKsB6vgFET7uE51L_-U9jC3JElrtcqVaVioHVE72-uffNc1SoN-tnXfrrmhjj-WqPdFwDj9zVSES1cOn_3xlUoysg7rij6mf1hVBBGgA7q9CcEtZLqZgcg_Gkhl156WuDSDAjViMi8OY2Mv4e4JUFiPRwTsgHRCuPkaUp87z?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/ML_vsD2xr2VQJFV-DtrJTthcUKlleQzkkvfhznZj2Kyqj5T2nHdSe5ZwIWq2TgFlu0vB7qO-5m1F_m7z3m0aJjjHdq3B9_NwSNdQm9KsugfSDrla3awjnfM_JTsgM53DCPRwo-E_IhG3cmsHkQIg98C4Y5-0fEIGryRdUQNuUyi20aNQj1zyNucW1qUU4Qdc?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/2M_7k3nlO08zN2rolyN0aKJlWqXT5oYVo8RzuGbEbMXelRsFEe74r-R9WX-AuTgORZSC8Xa0-6xdff6-wK2Q3PegQ0hSSrzDM-CQXwvPiNN_Z6taUYGNlg5Av-8rGZmQcuwieur3tdfCMjvZvd-_M44NOm8PA2lXkgj1XZtCQ8EdHo-NhXTNV3vqktzZuGAB?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/0dEdH6Mmn67VKSmXtFspXp8AzZD9mclGG98QKnbZTcV7DJEVwF3qXyMmT4xHXTE-PnR4ayuf6zAVxzlJmreNNceVvB6c_YAhwSX20IXxaNxfBFg9Xk0HNTxtOWs5OVzAPQKyiDKrcjWIgicRG6SCQGJBUmSNyOzLeIlQPbzyB37j6aBtKcAr92dTIPbxndn4?purpose=fullsize" width="150" />

### Concepts

* Region
* Availability Zone
* Multi-AZ
* Multi-Region
* Auto Scaling
* Managed Services
* Serverless
* Containers
* Kubernetes
* Infrastructure as Code
* Immutable Infrastructure

### À comprendre

Le System Design moderne est fortement lié au Cloud :

**Application → Container → Kubernetes → Load Balancer → Cloud → Multi-AZ → Multi-Region**

---

# 22. 🧰 Design Patterns utiles au System Design

<img src="https://images.openai.com/static-rsc-4/qzeXiYKx0AlEx6q_nzxkKv0Gl1mnpzh1gtSCLOteWpWrLjiUweWpsXgYeTk10f76bQD81Jq3vGDjERMQLbER08k2lC47UenHGlgkMYSA2ShAWHEzXLzBp105G1Wd7tdjTb-3Jc60NPciOHxg_3zAOI3FlX8uAulhr7Hlcf507GvMNi7eFlMGCuK0iz8EwPPb?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/BV-2iuXq34eOkGUX7GzT3VfN9eAe2U-PNqsX67poIiNd6e4BbNZYgJFFxU2DDlhzLSjgqK8uLrUgEKG1CffO4HWYAf3X_5k10fRKCJBeYd5bl2zYJ-DVQNB6Qb7d44eJGqeECx9c8RIWDecz-BtTL-nuzj_TPk1ZaZEDvHu6MJ7r0U5E9BowAhLDbyNzdmEl?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/cJ7ews9U3EiHPlxarVjuwzDxoRM445CRZCkzqTw_8haY6x2f_s5FBoUHIM9lan9WBK1NVU4KrglrRNNKXFPucHJOPu5GgZir_hct9aDN8hSbwT32AlSOBeomOckx7bL8dLPk6mJpue47QK2BXDhJFuWS74ny9GElUEP3cbyaOatkbqA7Gx_hRYSzXFSqK2JZ?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/AEFleJHlWmRIcmpR2TC2pFrdDzEsmVGEd9zZaCpLQSblCkP4ybK5ktpzcX1zZY6eTiLzmA7QUHOTE_H9VpfU6x1ZhyFZKEFVjXuoGNZa8yB1EP721A74KOLk1SD-hlwWaA1Lu4fVu07oFKrDx53BTB09NqR5QEWPE8Nr2206jmykYLDw_zZZvvp2lnduXd5N?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/1zSIiloGZDD3RB3gJM2dQ-VNfMoU99zW-ySeOXRhs7VmFBYF31hRVWg8wnWD2SWXbpnzNWc8YDm6XKjHkeyRoO3ye79xqQGfVPpIeLMGHG79ktyeoXppOuYk4zfTn42x8QF_9caF1EtMT0axNrvbvLjQQuEcPBaaQ3LHUZX3FC8Nl9Mk9hKptTNFiiYiqWyx?purpose=fullsize" width="150" />

### Patterns applicatifs utiles

* Factory
* Adapter
* Facade
* Proxy
* Observer
* Strategy

### Patterns distribués prioritaires

* **Circuit Breaker**
* **Bulkhead**
* **Saga**
* **CQRS**
* **Event Sourcing**
* **Transactional Outbox**
* **Strangler Fig**
* **Sidecar**
* **Retry**
* **Cache Aside**
* **Leader Election**

Pour le System Design, les patterns distribués sont généralement beaucoup plus importants que de mémoriser l'ensemble des GoF Design Patterns.

---

# 23. ⚖️ Théorèmes & compromis d'architecture

<img src="https://images.openai.com/static-rsc-4/rpTQzvjr1Agn-MluyfBOKjKNGcLSpkpxUM2JOUYe6Doo1cUxOFlo8igE4fcxWSbNaSf1c8ZqpudZmZK01PUVLiS20UUx2KqVkH1DMZQM6rh2NKYCda0USwKoyaDXI0gOxWglTiOyHEpvJHA2Oxhy2UlzZsd_WdlDe0aJbl5OryIiyJ-ThsjWQ3_nimgyb9qV?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/6qJiypKfMCHiEFP53AofFZIchMW18gXD9GTmy7eOECzYVOIpO-HWoUMpkfdCKUdjLoOp_aa_eIZtmWZe7Qb3wElfmMjGND8c5mKDCO9DvnDWuVsMJd-BignEmF6kF_0DLUaFyCl2fJcLrKa0_x7hQDO8sx6K5FcCfKpgyVg-AQ33hSULbABkecZ0o5NF3rY6?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/dAp1xq2nP-sUgyPEYoNF7IHYj-9PVrdeDTJ4O8KxqFVvZva8ZlLhnr2KxqRa_a-3EuuFbIvDErS9rSM1-PGD8BCsaygbhPUO1ghTKuMl0-k5j1nqK-E7aSI2DXwTdGcK5Y2t2gJ5G74J9ykT846PtLb71-9Crzkhx9KA8Byn0NsEz6Q1zaZbSEyRcobjd39B?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/k1dyxlxhErUvJdOP0TMpyAiARBGD3REAWY7Hl9zZcjsuwlchkHKv_gDklgJwAspnPTxjSUOZmp6DFlBqzodwTPRJlwhiMeY6VEWlOJlUaOrPGMebBdC4yiIkRsgUw_e6eA_p0buZ62AUgzbBAvPlEbYjf7Q6rtejoSGeL3N3DGpIdrYonbcmqmECBsFiuJGn?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/s8kKd4j18RSrpCgXC5IaTJw7UjEVYjNzS1C1buO7kn7GT3KNy7x62diWpEfUvY9t5pzjtOQAgShSDAO-2LZUsyolierbolXTlrbcNH2uMO4tggXl6miWZPgEoSWxzbGMdH4ewjboOB0GlLa_aDe9UEj-4ge2xF-Fs1gD2MSnOa3KlRtxAlh3yq_jfoKPO7tB?purpose=fullsize" width="150" />

### CAP Theorem

Un système distribué soumis à une partition réseau doit arbitrer entre :

* **Consistency**
* **Availability**

Partition Tolerance est la contrainte à laquelle le système distribué doit faire face.

### PACELC

Complète CAP en considérant également le fonctionnement normal :

**Partition → Availability vs Consistency**

**Else → Latency vs Consistency**

### Trade-offs à savoir expliquer

* Consistency vs Availability
* Latency vs Consistency
* Scalability vs Complexity
* Performance vs Maintainability
* Cost vs Availability
* Normalization vs Performance
* **Synchronous vs Asynchronous**
* **SQL vs NoSQL**
* **Stateful vs Stateless**
* **Monolith vs Microservices**
* Push vs Pull
* Polling vs Event-Driven

---

# 🗺️ Roadmap d'apprentissage

L'objectif n'est surtout pas d'apprendre les 200 notions précédentes simultanément.

## 🟢 Niveau 1 — Fondamentaux

Commence par :

**Architecture**
→ Monolith, Layered, Clean/Hexagonal, Microservices

**Scalabilité**
→ Stateless/Stateful, Horizontal/Vertical Scaling, Load Balancing

**API**
→ REST, Reverse Proxy, API Gateway

**Data**
→ SQL/NoSQL, Indexing, Replication, Sharding

**Cache**
→ Cache Aside, Redis, TTL

**Distributed Systems**
→ CAP, Consistency, Availability

**Communication**
→ Synchronous/Asynchronous, Message Queue

**Fiabilité**
→ Idempotency

---

# 🟡 Niveau 2 — Software Engineer confirmé

<img src="https://images.openai.com/static-rsc-4/YNWV1_I2BaBEMtW4tXvDuad4PpsN1GZReAB3q0EbjWGwNlRHS6IlCWwHmTSwqoGui4ELyH7ZFzJXKAfQCPG_cViFgw3L5GyjHvHPMSLSTMclmRXH7L2d43DA5-DW_tHB9taeeHkZfh16lZmH8kh-JgI3xS50Rfh0laJqSrnC9eyG72GnSZzRw5QEMlOoa4fx?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/0l5zeNt41mowcBTqRw6lcOEcSQftyw27XLp3NoUC381UcZy3PqkutOjRSYTkwXhW0JNKvmRSQdOh06VjN4KiIxd5lI5fUAVAA18th4zPnFdg7F7BT9vq3tHnkP-rj-nzMTZ1xcWokS3oOFg9tAnkYhjv7ziQsydJsYVkrezL87vvhfO6PEYpV8Z6PbSsXV2h?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/41W6dZikkVsNHhtCybbiyr3JS3S6vAvcnw535DuOgzOhPzNFsVNxyHQnfsi7-PuCh9JKYSVSEpVa4YmTNO577oN-GtnBJsO2NGXS9j_IkA2FzjmZ17t3FEibiR9ky-VPgoeQXwzAPp4tUoAHJ-xw4Xpg9pIXy4AXRxcXErCzKkU55wb0fuvyfstiVdJySlBP?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/J9rkNAi1uQ2e3Dxw-n4uJy2d6zV6Km-wxPGo_8R2IWZ9r6HsJO426r3_ZkP6ifLGB8X-yHzFDFJCT0VDeCcoIDc1aAkg22fO3Qzs1rDora5a5XPvJrpQn3t7fcyDhhyL3kGURKLSHAcGz_fgR8CCv2bR7wipc40w-Lj_F5d5ohYjRmz6031x2tbLV7mTDrYb?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/7p7cWhhTFj-kiooooXyqDIXKoKXDaTYit_LEn4VTpDhqs2bMb0L_89l_sH9BSdYYY4Ztn0bTVSfzZ9n1VEbtDs6YsOc6YwX57FKemosD_VMqyVu-P6vc5tysFtY5dH0j-6CyaCwGt2Ff7-2l2WOfSwaNNI7mrukgr_y_Eewy4nCDCLF6fLgqSNUC8_EJKH7U?purpose=fullsize" width="150" />

Ensuite :

* Circuit Breaker
* Retry + Backoff + Jitter
* Timeout
* Bulkhead
* Event-Driven Architecture
* Kafka / RabbitMQ
* CQRS
* Saga
* Transactional Outbox
* Eventual Consistency
* Service Discovery
* BFF
* Database per Service
* Observability
* Distributed Tracing

---

# 🟠 Niveau 3 — Senior / System Design Interview

<img src="https://images.openai.com/static-rsc-4/ijmaYp-2Y3lW_yABvpHMrKuZKkhqJgqv--25gm3A6FcPNEn9FvVcYdLQT1WvECNwztSOtcCFjXH-9jWp4OlpQdysEf6R5qCtzGYkikuTzv9u0X1PcJGEf891kUMcH2esK1d7uLl4w-c_KQS8edUVDEz0xLkszgxPoawrjwREzc_ut5Nfxconv-MdKwW6K16d?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/-7mLH1o3R35Dd3QMoqXD18zhMrbNLshXINAzUVSkwwS4KIV2TW7wplS1BOHa8ZxgRQmSg2V7jb6k8TnvOKX0Ur6PUt5C1C7DGcyWrdfdynXbMWfqiup7xgoJLyJVX1U0ZgkgRI363XNTHEqHiY1eFJQ41mJuTEPij3yYtId_lvIk5Ze8v8Tp-EJ3-j8HsTns?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/aKZGF6EZ5-Bw7eDdAstFeECD02h7U9efGAHzDrFft8t4SKqTs5C0HjldH3qDypg4xNBrbA3kHDEj_8uCk53vQNk4HxObC0j2ZwQP5aVnKnONAJQv3DyfJD_MzByShePa2P2dLffTTYoyLxigV4p13ggxmaiWp_AZ25Ip-H5BtD1PmR6gijarjcamfZowSyuG?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/0sVkoYni471cHtviAsr9c8lEurQ9G4nMQLl-djA1OaZTOlap8tEwLbhekLeRVl2OruBbmvKuHLKmwVJ4DMmKamJAF5am2dZ6tV0UjNSMTz36CgL2WJ0KhvBfG4S12lqL5fly7IlQuvb8TQFuIrXXQtCLmdUOwR2gobsjZekIEnPmL2hJxbd2CAKoKM1xCiIy?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/CNjxm16Q77IKJKZzhb39lIRdUoQpq-xqD4g9ZxRPAtFAeAGBdl-M9H5SY0B18Jw_MeXcPlzmGQ0Hjm9StbtRu3OujoZqzFVMy6EJs6_fbIRWGrzbBu9iCXnz9IqU8GaIZ5RZzyQfIsAU6RD2MSK5vXqd0HNV-0m02xvpCyrJhm6jmNMCPosShoYM7pYwNNQt?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/8qNMjh4WjG_2ZEJyTty5r9H5DuSE9BvjgXi6_Hgdyo6Wqvvx_sDq1F-_ijNl07XAwTjlFLg_b1SrkY5Ffobm8epGy6R4EQ5aTpyuVQHcdA-J0EUSHt0UIIOuI3nkCurUw6PB9nLEx_5yjEEn_AvN9GIqzKVrSHaHPnguoKpGfXlmTK5Xv4sQx5dn4mvV_egT?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/RlCShHSmcwVJKYlAIf7Ijd8b-HvMytPt4rX_BDU4cDgeSifJiOQmntIcWY2rEvfwuaV60PiS1YxaahdWUUfS4ixQYfWPOrhMMFZFkDHsM6JATvoaIOu8jPYInHZ3WeWEbnGAOAzHJyfjw2pPDqqU5o4jJ2Q-UEEpKPYH7B6azKWc-q_C7WtxBVr9rR6OeeZ7?purpose=fullsize" width="150" />

Approfondis :

* Consistent Hashing
* Leader Election
* Distributed Consensus
* Quorum
* Event Sourcing
* CDC
* Multi-Region Architecture
* Failover
* PACELC
* Distributed Locks
* Exactly-Once Semantics
* Backpressure
* Hot Partitions
* Disaster Recovery
* RTO / RPO
* SLI / SLO / SLA

---

# 🔴 Niveau 4 — Staff Engineer / Architect

<img src="https://images.openai.com/static-rsc-4/vhNU3H0xxLJehshF_MyM_gveRGU9IH5LBkylRI_Z1cngcrSISwWz351sOxbyVFZOduOaiUC3tcQrT5FSNrsA8Ibz6yMJ9aEaVAabfulVM2tiwbukAXVKtLO7vtu3yHdpbFU1CwE8sHMXJKgJCkccWyQef6oJnXO28XqGX2d49t3Xvfmd74HdziGNdHviKCMU?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/wE6CQ53FE8_duUQ7dYXHFA896-c5fxEXyhNJRqdXUNxORSnchmfwS70IYkjlZ1q4plf0PAcKxEV5BRKLjGx5vdSBI8aIqmQuRmGcv6CpDMfnA2I4LbZ5kH_zMgNae4JlFabUmbou13MQicI2A580hl8ZWLvY8vvuh-5r0CPclmBMf1cto5WfHtATmvkZHqKG?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/SoHMg7GE98WARwLUz6vPOM5BfQqfOZTMt3kgC6WZQiX2JeUew39YW_fCcTQUEKHbpL28D02Gv2NZQlVuRPT-UxbgcdMCm68DOkJUYpntZg5LDCdHp6zZUM4E7BHP6TENn506YOtfz08ZsvirboZ_mE2VMmG6B2-aMyrKv0_h-NdsGzzShdTL1s9P5KUwAnQl?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/-cA55b8N9KqiMUPJomIJUK_arIgSEayihNtgUuMTFKTgYkcjeqbnCwr2xPhvUsHQB_xKqVNzbyLXASotkdd9dGu5epJrNNB9cL3c8Bzf1PzyNkwrKFvDySzSmZy7xmojDPBzxAEbMwNLl0MGC-dBuLV9gf3HDbRngft4NGAzbKcw2i5BwgBQdKezDA-tKr1K?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/1mlZiWVA4ST1pzUWRV4E0vV7z2c_Rgo7PCZy3bPv1tOQLsvivBfG0lLNjtTS-EkbxOWShC2QspkTLsV3gBW19EPBY2E0KTV4OKiWk_0fhw3sAC1L9XDx8YC539N_-9DkvaVzqGhU80kc84kybQkn-PA2XbA4WBHXEIL6OdQRxJQwt6m3t-8CF_Bbhg1I2k4V?purpose=fullsize" width="150" />

<img src="https://images.openai.com/static-rsc-4/Xfihbs_AZ3obLHysLVn0tl4p4t34mvRNFzcMI7j6Swc-_Qs3WKzTF2EIWwg_oNheWE1TJpCQPHXdTyAkdFr3n-z_T-2Ri-IUT80wm2Yg3wMttjd9CoBKw2M1Xm5cW_dqvV4oTDwXYsQLOAI0BvUyHQ0SUrNQUxtPLLuUK_RroR0e-3al5njWjeCQWAeRW92M?purpose=fullsize" width="150" />

Enfin :

* Domain-Driven Design
* Bounded Context
* Context Mapping
* Anti-Corruption Layer
* Strangler Fig
* Cell-Based Architecture
* Multi-Tenancy
* Control Plane / Data Plane
* Zero Trust Architecture
* Architecture Decision Records — ADR
* Evolutionary Architecture
* Platform Engineering
* Multi-Region Active/Active
* Data Sovereignty
* Capacity Planning

---

# 🎯 Ordre que je recommande

Pour éviter de partir dans tous les sens, suis cette chaîne :

**1. Architecture**
→ **2. HTTP/API**
→ **3. Load Balancing**
→ **4. Scalabilité**
→ **5. Cache**
→ **6. Base de données**
→ **7. Replication & Sharding**
→ **8. Distributed Systems**
→ **9. Messaging**
→ **10. Résilience**
→ **11. Event-Driven**
→ **12. Transactions distribuées**
→ **13. Microservices**
→ **14. Observabilité**
→ **15. Sécurité**
→ **16. Cloud / Kubernetes**
→ **17. System Design avancé**

Pour un Software Engineer travaillant notamment avec **Java/Spring Boot, Angular, PostgreSQL, Docker/Kubernetes et des architectures distribuées**, cette progression permet de relier les concepts de System Design à des situations que tu peux réellement implémenter plutôt que de simplement mémoriser les définitions.
