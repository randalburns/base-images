FROM ubuntu:latest
MAINTAINER Gigantum <hello@gigantum.io>

# Install system level dependencies
RUN apt-get -y update
#apt-get -y upgrade
RUN apt-get -y install build-essential python3-dev python3-pip git curl vim

# Setup Global git config
RUN git config --global user.email "noreply@gigantum.io"; git config --global user.name "Gigantum AutoCommit"

RUN pip3 install numpy
RUN pip3 install matplotlib
RUN pip3 install requests
RUN pip3 install jupyter
RUN pip3 install jupyterlab
RUN pip3 install ipywidgets
RUN pip3 install pandas

ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
WORKDIR /mnt/labbook

CMD ["tail", "-f", "/dev/null"]
#CMD ["jupyter", "lab", "--ip=0.0.0.0", "--NotebookApp.token=''", "--no-browser", "--ConnectionFileMixin.ip=0.0.0.0"]
