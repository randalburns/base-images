FROM ubuntu:latest
MAINTAINER Gigantum <hello@gigantum.io>

# Install system level dependencies
RUN apt-get -y update
RUN apt-get -y upgrade
RUN apt-get -y install build-essential python3-dev python3-pip git curl vim
RUN apt-get -y install supervisor curl gosu

# Setup Global git config
RUN git config --global user.email "noreply@gigantum.io"; git config --global user.name "Gigantum AutoCommit"
# Install JupyterLab
RUN cd ~; curl -sL https://deb.nodesource.com/setup_6.x | bash - ; apt-get -y install nodejs
RUN pip3 install jupyter jupyterlab ipywidgets
RUN jupyter nbextension enable --py widgetsnbextension
RUN jupyter serverextension enable --py jupyterlab --sys-prefix
RUN jupyter labextension install @jupyter-widgets/jupyterlab-manager

RUN pip3 install numpy
RUN pip3 install matplotlib
RUN pip3 install requests
RUN pip3 install pandas

CMD ["true"]

