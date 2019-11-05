## Seleniun Docker in Python Enviroment 

This repo will guide through the tutorial to run headless chrome selenium driver inside your docker container. To make the process more simple we have setup docker-compose to build container with single command. Below is step by step guide to setup container and also python shell example to get page source of google.com using selenium.

### Selenium Docker

Here is the Docker file content which will be build by docker-compose 

```bash
FROM python:3.6.5

MAINTAINER AMAN SRIVASTAVA

# assuming repo is up to date on host machine
RUN apt update -y && apt-get install -y \
    build-essential \
    vim \
    libicu-dev \
    build-essential \
    libpcre3 \
    libpcre3-dev && \
    pip install pip --upgrade

# create unprivileged user
RUN adduser --disabled-password --gecos '' myuser  

# install google chrome
RUN wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add -
RUN sh -c 'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google-chrome.list'
RUN apt-get -y update
RUN apt-get install -y google-chrome-stable

# install chromedriver
RUN apt-get install -yqq unzip
RUN wget -O /tmp/chromedriver.zip http://chromedriver.storage.googleapis.com/`curl -sS chromedriver.storage.googleapis.com/LATEST_RELEASE`/chromedriver_linux64.zip
RUN unzip /tmp/chromedriver.zip chromedriver -d /usr/local/bin/

# set display port to avoid crash
ENV DISPLAY=:99
```

### Step up docker

1. **Install docker **

   ```bash
   $ wget get.docker.com
   $ bash index.html
   # add your current user to Docker group
   $ sudo usermod -aG docker $USER
   ```

2. **Install docker-compose**

   ```bash
   $ sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
   $ sudo chmod +x /usr/local/bin/docker-compose
   ```

3. **Clone repo and build and up container**

   ```bash
   $ git clone https://github.com/upraisedco/python-selenium-docker.git
   $ cd python-selenium-docker
   $ docker-compose up --build -d
   ```

4. **Command to get inside docker**

   ```bash
   $ docker exec -it python-selenium-docker_selenium_1 bash
   ```

### Python shell example inside container

```python
# tutorial to get page source of google.com
>>> from selenium import webdriver
>>> chrome_options = webdriver.ChromeOptions()
>>> chrome_options.add_argument('--no-sandbox')
>>> chrome_options.add_argument('--headless')
>>> chrome_options.add_argument('--disable-gpu')
>>> driver = webdriver.Chrome(chrome_options=chrome_options)
>>> driver.get('http://www.google.com')
>>> print(driver.page_source)
```

Thanks !!

Do reach out to me at amans.rlx@gmail.com if you encounter any issues.
