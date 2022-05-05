FROM nvidia/cuda:11.4.3-cudnn8-devel-ubuntu18.04

# Use New Zealand mirrors
RUN sed -i 's/archive/nz.archive/' /etc/apt/sources.list

RUN apt update

# Set timezone to Auckland
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get install -y locales tzdata git
RUN locale-gen en_NZ.UTF-8
RUN dpkg-reconfigure locales
RUN echo "Pacific/Auckland" > /etc/timezone
RUN dpkg-reconfigure -f noninteractive tzdata
ENV LANG en_NZ.UTF-8
ENV LANGUAGE en_NZ:en

# Create user 'kaimahi' to create a home directory
RUN useradd kaimahi
RUN mkdir -p /home/kaimahi/
RUN chown -R kaimahi:kaimahi /home/kaimahi
ENV HOME /home/kaimahi

# Install apt packages
RUN apt update
RUN apt install -y awscli curl software-properties-common ffmpeg
RUN add-apt-repository ppa:deadsnakes/ppa

# Install python
ENV PYTHON_VERSION 3.9
ENV PYTHON python${PYTHON_VERSION}
ENV PIP ${PYTHON} -m pip
RUN apt update
RUN apt install -y ${PYTHON}-dev ${PYTHON}-distutils
RUN rm /usr/bin/python3 && ln -s /usr/bin/${PYTHON} /usr/bin/python3
RUN curl -sS https://bootstrap.pypa.io/get-pip.py | ${PYTHON}

# Install pip packages
RUN ${PIP} install --upgrade pip
COPY requirements.txt /root/requirements.txt
RUN ${PIP} install -r /root/requirements.txt

# Install local package
COPY deepspeech /code/deepspeech
COPY setup.py /code
RUN ${PYTHON} -m pip install -e /code
ENV PYTHONPATH="/code:${PYTHONPATH}"
