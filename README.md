# MongoDB Payments Write & Read Concern Tester Project

Processes simulated Payments records using MongoDB. Provides ability to ingest randomly generated records into a collection with one of various Write Concerns and the ability to read the ingested records back out using one of various Read Concerns. Processing times and response times for a sample of the generated requests are appended to a log file.

_Note:_ If you specify the number of client injection processes as exceeding 6 less than the total number of hardware threads (vCPUs) of the host machine/VM then the result data logged to file may not fully appear due to a suspected Python multiprocessing issue.

For usage first ensure the '.py' script is executable and then run:
```
./payments-records-loader.py -h
```

Example to connect to an Atlas cluster to write data to using 8 processes:
```java
./payments-records-loader.py -u "mongodb+srv://myuser:mypswd@testcluster-ap.mongodb.net" -p 8
```

processing-output.log
```java
0% - 0 documents inserted for data set id 0 - 2022-01-04 21:22:43.145228 - sample response time for one request: 182.10816383361816 ms
1% - 10000 documents inserted for data set id 0 - 2022-01-04 21:26:02.443885 - sample response time for one request: 8.154869079589844 ms
2% - 20000 documents inserted for data set id 0 - 2022-01-04 21:29:19.510490 - sample response time for one request: 8.11910629272461 ms
3% - 30000 documents inserted for data set id 0 - 2022-01-04 21:32:36.540177 - sample response time for one request: 9.242057800292969 ms
```

# Running MongoDB on ChromeOS (via Crostini) and inserting bulk data to MongoDB Atlas or locally

https://pauldone.blogspot.com/2019/12/running-mongodb-on-chromeos-via-crostini.html

By [Paul Done](https://pauldone.blogspot.com/) 

In my previous post I explored Linux application support in ChromeOS and Chromebooks (a.k.a. Crostini). Of course I was bound to try running MongoDB in this environment, which I found to work really well (for development purposes). Here's my notes on running a MongoDB database and tools on a Chromebook with Linux (beta) enabled:
- In ChromeOS, launch the Terminal app (which opens a Shell inside the 'Penguin' Linux container inside the 'Termina' Linux VM)
- Run the following commands which are documented in the MongoDB Manual page on installing MongoDB Enterprise on Debian (following the manual's tab instructions titled “Debian 9 "Stretch”):

```java
wget -qO - https://www.mongodb.org/static/pgp/server-4.2.asc | sudo apt-key add -
echo "deb http://repo.mongodb.com/apt/debian stretch/mongodb-enterprise/4.2 main" | sudo tee /etc/apt/sources.list.d/mongodb-enterprise.list
sudo apt-get update
sudo apt-get install -y mongodb-enterprise
Start a MongoDB database instance running:
mkdir ~/data
mongod --dbpath ~/data
```

Launch a second Terminal window and then run the Mongo Shell against this database and perform a quick database insert and query test:

```java
mongo
or mongosh

db.mycoll.insert({a:1})
db.mycoll.find()
db.mycoll.drop()
exit
```

## Install Python 3 and the PIP Python package manager (using Anaconda) and then install the MongoDB Python driver (PyMongo):

```java
wget https://repo.anaconda.com/archive/Anaconda3-2019.10-Linux-x86_64.sh
bash Anaconda3-*-Linux-x86_64.sh
source ~/.bashrc
python --version
pip --version
pip install --user pymongo
```

Test PyMongo by running a small ‘payments data generator’ Python script pulled down from a GitHub repository (this should insert records into the MongoDB local database’s “fs.payments” collection; after letting it run for a minute, continuously inserting new records, press Ctrl-C to stop it):

```java
git clone https://github.com/pkdone/PaymentsWriteReadConcerns.git
cd PaymentsWriteReadConcerns/
./payments-records-loader.py -p 1
```

Download MongoDB Compass (use the Ubuntu 64-bit 14.04+ version), install and run it against the 'localhost' MongoDB database and inspect the contents of the “fs.payments” collection:

```java
wget https://downloads.mongodb.com/compass/mongodb-compass_1.20.4_amd64.deb
sudo apt install ./mongodb-compass_*_amd64.deb
mongodb-compass
```
