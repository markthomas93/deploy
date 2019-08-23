FROM jupyter/base-notebook
# create app directories

ARG IPYWIDGETS_VER=7.5.1
ARG GECKO_VER=v0.23.0
ARG SELENIUM_VER=3.141.0
ARG RISE_VER=5.5.1
ARG NBEXTENSIONS_VER=0.5.1
ARG NBCONFIG_VER=0.4.1



USER root

## Install ipywidgets (https://github.com/jupyter-widgets/ipywidgets).
RUN apt-get update \
  && apt-get install -y --no-install-recommends \
             libjpeg-dev \
             libgif-dev \
             libcairo2-dev \
             pkg-config \
             gnupg2 \
             curl \
  && pip install ipywidgets==${IPYWIDGETS_VER} \
  && apt-get autoremove -y \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*


## Edgecase: set display port to avoid crash

ENV DISPLAY=:99

## Install the latest versions of Google Chrome and Chromedriver:
RUN export DEBIAN_FRONTEND=noninteractive \
  && apt-get update \
  && apt-get install \
    unzip \
  && \
  CHROME_URL=https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb \
  && curl -sL "${CHROME_URL}" > /tmp/chrome.deb \
  && apt install --no-install-recommends --no-install-suggests -y \
    /tmp/chrome.deb \
  && CHROMIUM_FLAGS='--no-sandbox --disable-dev-shm-usage' \
  # Patch Chrome launch script and append CHROMIUM_FLAGS to the last line:
  && sed -i '${s/$/'" ${CHROMIUM_FLAGS}"'/}' /opt/google/chrome/google-chrome \
  && BASE_URL=https://chromedriver.storage.googleapis.com \
  && VERSION=$(curl -sL "${BASE_URL}/LATEST_RELEASE") \
  && curl -sL "${BASE_URL}/${VERSION}/chromedriver_linux64.zip" -o /tmp/driver.zip \
  && unzip /tmp/driver.zip \
  && chmod 755 chromedriver \
  && mv chromedriver /usr/local/bin/ \
  # Remove obsolete files:
  && apt-get autoremove --purge -y \
    unzip \
  && apt-get clean \
  && rm -rf \
    /tmp/* \
    /usr/share/doc/* \
    /var/cache/* \
    /var/lib/apt/lists/* \
    /var/tmp/*

# set display port to avoid crash
ENV DISPLAY=:99


## Install Selenium python package
RUN pip install --no-cache selenium==${SELENIUM_VER}

## Edgecase: Local installs have a small issue where Jupyter did not add in the following:
RUN pip install --no-cache rise==${RISE_VER}
RUN conda install -c conda-forge jupyter_nbextensions_configurator && \
    conda install -c conda-forge jupyter_contrib_nbextensions && \
	jupyter nbextension enable freeze/main 

## Create the custom Jupyter directories

WORKDIR ${HOME}

## Only needed when sharing via Github
## And deploying via MyBinder.org
## Copy repo files over

COPY . ${HOME}

## And make sure they are owned by the notebook user...
RUN chown -R ${NB_USER} ${HOME}

## Reset the container user back to the notebook user
USER ${NB_UID}

## And make sure they are owned by the notebook user...
RUN chown -R ${NB_USER} ${HOME}