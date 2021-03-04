# Basic Redis Leaderboard Demo Java

Show how the redis works with Java, Spring.

## Try it out

<p>
    <a href="https://heroku.com/deploy" target="_blank">
        <img src="https://www.herokucdn.com/deploy/button.svg" alt="Deploy to Heroku" width="200px"/>
    <a>
</p>
<p>
    <a href="https://deploy.cloud.run?dir=server" target="_blank">
        <img src="https://deploy.cloud.run/button.svg" alt="Run on Google Cloud" width="200px"/>
    </a>
    (See notes: How to run on Google Cloud)
</p>


## How to run on Google Cloud

<p>
    If you don't have redis yet, plug it in  (https://spring-gcp.saturnism.me/app-dev/cloud-services/cache/memorystore-redis).
    After successful deployment, you need to manually enable the vpc connector as shown in the pictures:
</p>

1. Open link google cloud console.

![1 step](docs/1.png)

2. Click "Edit and deploy new revision" button.

![2 step](docs/2.png)

3. Add environment.

![3 step](docs/3.png)

4.  Select vpc-connector and deploy application.

![4  step](docs/4.png)

<a href="https://github.com/GoogleCloudPlatform/cloud-run-button/issues/108#issuecomment-554572173">
Problem with unsupported flags when deploying google cloud run button
</a>

## How it works?

![How it works](docs/screenshot001.png)


# How it works?
## 1. How the data is stored:
<ol>
    <li>The AAPL's details - market cap of 2,6 triillions and USA origin - are stored in a hash like below:
      <pre> <a href="https://redis.io/commands/hset">HSET</a> "company:AAPL" symbol "AAPL" market_cap "2600000000000" country USA</pre>
     </li>
    <li>The Ranks of AAPL of 2,6 trillions are stored in a <a href="https://redislabs.com/ebook/part-1-getting-started/chapter-1-getting-to-know-redis/1-2-what-redis-data-structures-look-like/1-2-5-sorted-sets-in-redis/">ZSET</a>. 
      <pre><a href="https://redis.io/commands/zadd">ZADD</a>  companyLeaderboard 2600000000000 company:AAPL</pre>
    </li>
</ol>

<br/>

## 2. How the data is accessed:
<ol>
    <li>Top 10 companies: <pre><a href="https://redis.io/commands/zrevrange">ZREVRANGE</a> companyLeaderboard 0 9 WITHSCORES</pre> </li>
    <li>All companies: <pre><a href="https://redis.io/commands/zrevrange">ZREVRANGE</a> companyLeaderboard 0 -1 WITHSCORES</pre> </li>
    <li>Bottom 10 companies: <pre><a href="https://redis.io/commands/zrange">ZRANGE</a> companyLeaderboard 0 9 WITHSCORES</pre></li>
    <li>Between rank 10 and 15: <pre><a href="https://redis.io/commands/zrevrange">ZREVRANGE</a> companyLeaderboard 9 14 WITHSCORES</pre></li>
    <li>Show ranks of AAPL, FB and TSLA: <pre><a href="https://redis.io/commands/zrevrange">ZREVRANGE</a>  companyLeaderBoard company:AAPL company:FB company:TSLA</pre> </li>
    <!-- <li>Pagination: Show 1st 10 companies: <pre><a href="https://redis.io/commands/zscan">ZSCAN</a> 0 companyLeaderBoard COUNT 10 7.Pagination: Show next 10 companies: ZSCAN &lt;return value from the 1st 10 companies&gt; companyLeaderBoard COUNT 10 </li> -->
    <li>Adding 1 billion to market cap of FB company: <pre><a href="https://redis.io/commands/zincrby">ZINCRBY</a> companyLeaderBoard 1000000000 "company:FB"</pre></li>
    <li>Reducing 1 billion of market cap of FB company: <pre><a href="https://redis.io/commands/zincrby">ZINCRBY</a> companyLeaderBoard -1000000000 "company:FB"</pre></li>
    <li>Companies between 500 billion and 1 trillion: <pre><a href="https://redis.io/commands/zcount">ZCOUNT</a> companyLeaderBoard 500000000000 1000000000000</pre></li>
    <li>Companies over a Trillion: <pre><a href="https://redis.io/commands/zcount">ZCOUNT</a> companyLeaderBoard 1000000000000 +inf</pre> </li>
</ol>


## How to run it locally?

## Development

```
git clone 
```

### Run docker compose or install redis manually

Install docker (on mac: https://docs.docker.com/docker-for-mac/install/)

```sh
docker network create global
docker-compose up -d --build
```

#### Open directory server (cd server): copy .env.example to create .env (copy .env.example .env  or cp .env.example .env). And provide the values for environment variables (if needed)
   	- REDIS_URL: Redis server url
    - REDIS_HOST: Redis server host
	- REDIS_PORT: Redis server port
	- REDIS_DB: Redis server db index
	- REDIS_PASSWORD: Redis server password

#### Run backend

Install gradle (Use Gradle 6.3 or later) (on mac: https://gradle.org/install/) 


Install JDK (use 8 or later version) (on mac: https://docs.oracle.com/javase/10/install/installation-jdk-and-jre-macos.htm)

``` sh
cd server
export $(cat .env | xargs)
./gradlew build
./gradlew run
```

#### Run frontend

Static сontent runs automatically with the backend part. In case you need to run it separately, please see README in the [client](client) folder.

