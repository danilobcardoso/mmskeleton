FROM nvidia/cuda:10.0-cudnn7-devel-ubuntu18.04
     
RUN echo "Installing dependencies..." && \
	apt-get -y --no-install-recommends update && \
	apt-get -y --no-install-recommends upgrade && \
	apt-get install -y --no-install-recommends \
	build-essential \
	cmake \
	git \
	wget

ENV PATH /opt/conda/bin:$PATH

RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda.sh && \
    /bin/bash ~/miniconda.sh -b -p /opt/conda && \
    rm ~/miniconda.sh && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc

RUN conda install pytorch torchvision cudatoolkit=10.0 -c pytorch -y

ENV CPATH=/usr/local/cuda/include:$CPATH \
	LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH \
	DYLD_LIBRARY_PATH=/usr/local/cuda/lib:$DYLD_LIBRARY_PATH  \
	PATH=/usr/local/cuda/bin:$PATH


# RUN cd \tmp && \
RUN pip install torch-scatter==latest+cu100 torch-sparse==latest+cu100 -f https://s3.eu-central-1.amazonaws.com/pytorch-geometric.com/whl/torch-1.4.0.html && \
	pip install torch-geometric

RUN mkdir \aad-tmp && \
	cd \aad-tmp && \
    git clone https://github.com/danilobcardoso/abnormal-activity-detection.git


RUN conda install notebook -y
RUN conda install -c conda-forge pythreejs -y
RUN conda install -c anaconda cython

RUN apt-get update && \
	apt-get install -y libglib2.0-0 libsm6 libxext6 libxrender-dev

# Aqui é uma tentativa fake de instalação. Ela dará errado mas gerará algumas pastas necessárias para os passos seguintes
RUN cd /aad-tmp/abnormal-activity-detection/ && \
    python setup.py develop; exit 0

RUN mkdir -p /aad-tmp/abnormal-activity-detection/build/lib.linux-x86_64-3.7/mmskeleton/ops/nms && \
	mkdir -p /aad-tmp/abnormal-activity-detection/build/temp.linux-x86_64-3.7/mmskeleton/ops/nms && \
	cd /aad-tmp/abnormal-activity-detection/mmskeleton/ops/nms && \
	python setup_linux.py build && \
	cp /aad-tmp/abnormal-activity-detection/mmskeleton/ops/nms/build/lib.linux-x86_64-3.7/*.so /aad-tmp/abnormal-activity-detection/build/lib.linux-x86_64-3.7/mmskeleton/ops/nms && \
	cp /aad-tmp/abnormal-activity-detection/mmskeleton/ops/nms/build/temp.linux-x86_64-3.7/*.o /aad-tmp/abnormal-activity-detection/build/temp.linux-x86_64-3.7/mmskeleton/ops/nms

RUN cd /aad-tmp/abnormal-activity-detection/ && \
    python setup.py develop && \
	python setup.py develop --mmdet

EXPOSE 8888
