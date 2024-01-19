FROM continuumio/miniconda3:latest
LABEL maintainer="Aurelio Amerio"

RUN conda update -y conda

# add base packages
RUN conda install -y numpy scipy pandas matplotlib notebook ipython

# install wget and linux base dependencies 
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y wget bzip2 ca-certificates \
    libglib2.0-0 libxext6 libsm6 libxrender1 \
    git mercurial subversion tzdata && \
    ln -fs /usr/share/zoneinfo/Europe/Madrid /etc/localtime && \
    dpkg-reconfigure --frontend noninteractive tzdata

# install julia
WORKDIR /opt
RUN wget https://julialang-s3.julialang.org/bin/linux/x64/1.10/julia-1.10.0-linux-x86_64.tar.gz
# extract julia to the julia folder
RUN tar zxvf julia-1.10.0-linux-x86_64.tar.gz && \
    rm julia-1.10.0-linux-x86_64.tar.gz
#add julia to the path
ENV PATH /opt/julia-1.10.0/bin:$PATH

# system-wide packages
ENV JULIA_DEPOT_PATH=/usr/local/share/julia

# use system-wide conda
ENV JULIA_CONDAPKG_BACKEND Current

# install juliacall using pip
RUN pip install juliacall

# initialize juliacall
RUN python -c 'from juliacall import Main as jl; jl.println("juliacall installed!")'
# RUN python -c "import julia; julia.install()"

# install julia notebook extension
RUN julia -e 'using Pkg; Pkg.add("IJulia")' # add the julia kernel for jupyter

# cleanup 
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*


# user environment

# we hard-code the primary depot regardless of the actual user, i.e., we do not let it
# default to `$HOME/.julia`. this is for compatibility with `docker run --user`, in which
# case there might not be a (writable) home directory.
RUN mkdir -m 0777 /depot
ENV JULIA_DEPOT_PATH=/depot:/usr/local/share/julia

# cd to the opt directory 
WORKDIR /opt/workdir

# start the jupyter notebook as root
CMD ["jupyter", "notebook", "--ip=0.0.0.0", "--port=8888", "--allow-root", "--no-browser"]

