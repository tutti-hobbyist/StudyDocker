FROM nvidia/cuda:10.1-cudnn7-runtime-ubuntu18.04

RUN apt-get update && apt-get install -y \
    sudo \
    wget \
    vim

WORKDIR /opt

# AWS上にて、こちらの古いバージョンのLinuxのshファイルで docker build を実行すると、Unpacking payload...で画面が止まり、うまくビルド出来ない問題が発生
# RUN wget https://repo.anaconda.com/archive/Anaconda3-2019.10-Linux-x86_64.sh && \
#     sh /opt/Anaconda3-2019.10-Linux-x86_64.sh -b -p /opt/anaconda3 && \
#     rm -f Anaconda3-2019.10-Linux-x86_64.sh

# 下が新しいバージョンのLinuxのshファイル
RUN wget https://repo.anaconda.com/archive/Anaconda3-2020.07-Linux-x86_64.sh && \
    sh /opt/Anaconda3-2020.07-Linux-x86_64.sh -b -p /opt/anaconda3 && \
    rm -f Anaconda3-2020.07-Linux-x86_64.sh

ENV PATH /opt/anaconda3/bin:$PATH

RUN pip install --upgrade pip && pip install \
    keras==2.3 \ 
    scipy==1.4.1 \
    tensorflow-gpu==2.1

WORKDIR /

CMD ["jupyter", "lab", "--ip=0.0.0.0", "--allow-root"]