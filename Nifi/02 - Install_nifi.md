# **Install Nifi**

The steps to install **nifi** are: 

1. Update your server

        sudo apt update

2. Download Apache Nifi Using wget 
    
        wget https://dlcdn.apache.org/nifi/1.16.2/nifi-1.16.2-bin.tar.gz

3. Check where nifi is doswnloaded.
    
        ls

4. Unzip

        tar -xvf nifi-1.16.2-bin.tar.gz

5. Start Apache Nifi

        nifi-1.16.2/bin/nifi.sh start

6. Check niki is running.

        jps

7. Open in browser.

        https://localhost:8443/nifi 

9. Show my user and password in nifi-app_2022-06-13_15.0.log

        Generated Username [75d19c51-db90-4d17-8648-240efcff0821]
        Generated Password [rn33c+rkf3rXNb0Gz74Fhrkg/A9Gv+z8]