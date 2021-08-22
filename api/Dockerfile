FROM centos:centos7
ENV HOME /root
ARG project_dir

# workdir
# なければ新規作成
WORKDIR $project_dir
RUN yum update -y && \
    yum install -y gcc zlib-devel bzip2 bzip2-devel readline readline-devel sqlite sqlite-devel openssl openssl-devel git libffi-devel wget which

ENV PYENV_ROOT /root/.pyenv
ENV PATH $PYENV_ROOT/bin:$PATH

RUN wget https://www.python.org/ftp/python/3.9.0/Python-3.9.0.tgz && \
    tar xvf Python-3.9.0.tgz
RUN cd Python-3.9.0 && \
    ./configure --enable-optimizations && \
    make altinstall
# rootフォルダで実施
WORKDIR /root/
RUN curl https://bootstrap.pypa.io/get-pip.py | python3.9 && \
    git clone https://github.com/pyenv/pyenv.git /root/.pyenv && \
    echo 'eval "$(pyenv init -)"' >> ~/.bash_profile && \
    source ~/.bash_profile 
RUN mv /usr/bin/python /usr/bin/python_old && \
    ln -s $PYENV_ROOT/shims/python /usr/bin/python

RUN pyenv install 3.9.0 && \
    pyenv global 3.9.0 && \
    pyenv rehash
#upgrade pip
RUN pip install --upgrade pip
COPY ./requirements.txt /workspace/requirements.txt
RUN pip install -r /workspace/requirements.txt

RUN wget https://www.sqlite.org/2021/sqlite-autoconf-3360000.tar.gz && \
    tar xvf sqlite-autoconf-3360000.tar.gz
RUN cd sqlite-autoconf-3360000 && \
    ./configure --prefix=/usr/local && \
    make && \
    make install && \
    find /usr/ -name sqlite3
RUN mv /usr/bin/sqlite3 /usr/bin/sqlite3_old && \
    ln -s /usr/local/bin/sqlite3 /usr/bin/sqlite3
ENV LD_LIBRARY_PATH /usr/local/lib
RUN python -m pip install Django

# CMD [ "/sbin/init" ]
WORKDIR /workspace/test_api
CMD ["gunicorn", "--bind", "192.168.56.10:8000", "test_api.wsgi:application"]