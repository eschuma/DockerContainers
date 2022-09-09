FROM ubuntu:20.04

ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update --fix-missing && \
  apt-get install -y wget bzip2 build-essential lftp \
  ca-certificates git libglib2.0-0 libxext6 libsm6 \
  libxrender1 git mercurial nano subversion vim && \
  apt-get clean

RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda.sh && \
  /bin/bash ~/miniconda.sh -b -p /opt/conda && \
  rm ~/miniconda.sh && \
  /opt/conda/bin/conda clean -tipsy && \
  ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
  echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
  echo "conda activate base" >> ~/.bashrc && \
  find /opt/conda/ -follow -type f -name '*.a' -delete && \
  find /opt/conda/ -follow -type f -name '*.js.map' -delete && \
  /opt/conda/bin/conda clean -afy

ENV PATH=/opt/conda/bin:$PATH

RUN conda update -n base -c defaults conda

# install packages to base environment
RUN conda install mamba -n base -c conda-forge && \
#	conda install -n base -c conda-forge -c bioconda r-base r-phanghorn && \
    mamba install -n base -c conda-forge -c r -c bioconda python=3.8 \
#    mamba install -n base -c conda-forge -c bioconda python=3.8 \
	r-base -y

#RUN mamba install -n base -c conda-forge -c bioconda \
    #	r-base=4.2.* \
    #fastqc \
    #multiqc \
    #star \
    #trimmomatic \
    #trinity \
    #cufflinks \
    #rsem \
    #htseq \
    #tophat \
    #bioconductor-deseq2 \
#    bioconductor-edger \
    #r-pheatmap \
#    r-ggplot2 \
#    r-nlme \
#    r-phytools \
#    r-devtools \
#    r-viridis \
#    r-plyr \
#    r-tidyverse \
#    r-car \
    #r-openxlsx \
#    r-emmeans \
#    r-mumin \
#    r-ape \
#    r-phangorn \
#    bioconductor-ggtree \
#    bioconductor-annotationhub \
#    r-ggdendro \
#    r-gridextra \
#    r-gtable \
#    r-ggfortify \
#    r-foreach \
#    r-doparallel \
#    bioconductor-mygene \
#    r-testthat \
#    bioconductor-noiseq \
#    pyrpipe -y

## build is *much* slower than with mamba, but it gets around the bug of force installing r-base 4.1.3 overtop of r-base 4.2.1
RUN R -e "install.packages(c('ggplot2','nlme','phytools','plyr','viridis','emmeans','MuMIn','ggdendro','gridExtra','gtable','foreach','doParallel','testthat'), dependencies=TRUE, repos='https://cloud.r-project.org')"
RUN R -e "install.packages(c('statmod', 'ggpubr'), dependencies=TRUE, repos='http://cran.rstudio.com/')"
RUN R -e "install.packages('devtools', dependencies=TRUE, repos='http://cran.rstudio.com/')"

#RUN R -e "devtools::install_github('tidyverse/tidyverse')"
#RUN R -e "devtools::install_github('sinhrks/ggfortify')"

RUN R -e "install.packages('BiocManager', dependencies=TRUE, repos='http://cran.rstudio.com/')"
RUN R -e "BiocManager::install('limma')"
RUN R -e "BiocManager::install('GO.db')"
RUN R -e "BiocManager::install('topGO')"
RUN R -e "BiocManager::install('ROntoTools')"
RUN R -e "BiocManager::install('edgeR')"
RUN R -e "BiocManager::install('DESeq2')"
RUN R -e "BiocManager::install('ggtree')"
RUN R -e "BiocManager::install('AnnotationHub')"
RUN R -e "BiocManager::install('mygene')"
RUN R -e "BiocManager::install('NOISeq')"
RUN R -e "BiocManager::install('org.Dr.eg.db')"

RUN R -e "ROntoTools::keggPathwayGraphs('pki', verbose = FALSE)"
RUN R -e "ROntoTools::keggPathwayGraphs('dre', verbose = FALSE)"
RUN R -e "ROntoTools::keggPathwayGraphs('sfm', verbose = FALSE)"

RUN R -e "install.packages('reshape2', dependencies=TRUE, repos='http://cran.rstudio.com/')"

COPY /entrypoint.sh /opt/conda/bin/entrypoint.sh
RUN chmod a+x /opt/conda/bin/entrypoint.sh

# gatk4 example
# WORKDIR /app
# RUN wget https://github.com/broadinstitute/gatk/releases/download/4.2.2.0/gatk-4.2.2.0.zip
# RUN unzip gatk-4.2.2.0.zip
# lives at /app/gatk-4.2.2.0
# after adding to path
# picard.jar should be in PATH

#ENTRYPOINT ["/opt/conda/bin/entrypoint.sh"]
CMD [".", "/opt/conda/bin/entrypoint.sh"]