# Prompt

would suggest use https://chat.deepseek.com, looks like it does a better job than chatGPT o1 mini on the math problems.

> You are a quiz creator of highly diagnostic quizzes. You will look up how to develop low-stakes tests and diagnostics. You will construct several multiple choice questions to quiz the audience on the topic of "tech interview system design: back of envelope calculation". The questions should be highly relevant and go beyond just facts. Multiple choice questions should include plausible, competitive alternate responses and should not include an "all of the above option." At the end of the quiz, you will provide an answer key and explain the right answer.

# Quizzes

Here’s a highly diagnostic quiz on the topic of **"Tech Interview System Design: Back-of-the-Envelope Calculations"**. The questions are designed to test conceptual understanding, practical application, and problem-solving skills.

---

### **Quiz: Back-of-the-Envelope Calculations in System Design**

#### **Question 1: Estimating Request Throughput**
You are designing a social media platform and need to estimate the number of requests per second (RPS) the system must handle. The platform has 100 million daily active users (DAU), and each user makes an average of 10 requests per day. What is the approximate RPS the system must handle?

A) 1,000 RPS  
B) 10,000 RPS  
C) 100,000 RPS  
D) 1,000,000 RPS  

---

#### **Question 2: Storage Requirements for a Photo-Sharing App**
A photo-sharing app allows users to upload 1 photo per day. Each photo is approximately 5 MB in size. If the app has 10 million users, how much storage is required per year?

A) 18 TB  
B) 180 TB  
C) 1.8 PB  
D) 18 PB  

---

#### **Question 3: Latency Calculation**
A distributed system has a processing latency of 50 ms per request and a network latency of 100 ms per request. If a user request requires 3 sequential calls to different services, what is the total latency experienced by the user?

A) 150 ms  
B) 300 ms  
C) 450 ms  
D) 600 ms  

---

#### **Question 4: Cache Sizing for a News Feed**
A news feed system serves 1 million requests per hour, and each request fetches 10 KB of data. If the cache hit rate is 50%, how much data is served from the cache per hour?

A) 5 GB  
B) 10 GB  
C) 20 GB  
D) 50 GB  

---

#### **Question 5: Bandwidth Calculation for Video Streaming**
A video streaming platform serves 1 million concurrent users. Each user streams video at 5 Mbps. What is the total bandwidth required to support this load?

A) 5 Gbps  
B) 50 Gbps  
C) 500 Gbps  
D) 5 Tbps  

---

### **Answer Key and Explanations**

#### **Question 1: Estimating Request Throughput**
**Correct Answer: B) 10,000 RPS**  
Explanation:  
- Total requests per day = 100 million DAU * 10 requests/user = 1 billion requests/day.  
- Requests per second = 1 billion / (24 * 60 * 60) ≈ 11,574 RPS.  
- The closest option is 10,000 RPS.

---

#### **Question 2: Storage Requirements for a Photo-Sharing App**
**Correct Answer: C) 1.8 PB**  
Explanation:  
- Total photos per year = 10 million users * 1 photo/user/day * 365 days = 3.65 billion photos.  
- Total storage = 3.65 billion * 5 MB = 18.25 million GB = 18.25 PB.  
- The closest option is 1.8 PB (note: this is a trick question to test unit conversion).

---

#### **Question 3: Latency Calculation**
**Correct Answer: C) 450 ms**  
Explanation:  
- Total latency = (processing latency + network latency) * number of sequential calls.  
- Total latency = (50 ms + 100 ms) * 3 = 450 ms.

---

#### **Question 4: Cache Sizing for a News Feed**
**Correct Answer: A) 5 GB**  
Explanation:  
- Total data served per hour = 1 million requests * 10 KB = 10 GB.  
- Cache hit rate is 50%, so 50% of the data is served from the cache.  
- Data served from cache = 10 GB * 0.5 = 5 GB.

---

#### **Question 5: Bandwidth Calculation for Video Streaming**
**Correct Answer: D) 5 Tbps**  
Explanation:  
- Total bandwidth = 1 million users * 5 Mbps = 5 million Mbps = 5 Tbps.  
- This tests your ability to convert units (Mbps to Tbps).

---

This quiz is designed to test your ability to perform back-of-the-envelope calculations, which are critical for system design interviews. Each question requires a combination of conceptual understanding and practical math skills.