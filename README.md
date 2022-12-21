## Theory [2]

As usual, we will start with a few theoretical questions:

* [0.5] What is Docker, and how it differs from dependencies management systems? From virtual machines?

Docker — это платформа с открытым исходным кодом для создания, развертывания и управления контейнерными приложениями.

Docker отличается от других систем управления зависимостями своей гибкостью и безопасностью. Docker Hub — облачный сервис компании, который предлагает более 100 000 бесплатных приложений, общедоступных и частных реестров с официальными репозиториями от сторонних поставщиков: от Nginx и Ubuntu до MongoDB и Redis. Он разработан, чтобы быть легким и простым. Docker имеет хороший уровень безопасности. Docker очень популярен в использовании, поэтому при обнаружении ошибки есть шанс 0.99, что решение проблемы будет найдено в Интернете, и довольно быстро.

Хотя для каждой рабочей нагрузки на виртуальной машине (ВМ) требуется полноценная ОС или гипервизор, многие рабочие нагрузки могут выполняться на одной ОС при использовании Docker. Контейнеры Docker имеют быстрое время запуска и сокращают время загрузки. Виртуальные машины долго запускаются и имеют ужасную производительность. Поскольку контейнеры Docker меньше, чем виртуальные машины, проще перемещать файлы в файловую систему хоста. (Виртуальные машины должны иметь копию ОС и всех ее зависимостей, иначе размер образа увеличится, а совместное использование данных будет трудоемким). Поскольку ОС работает, приложения контейнеров Docker запускаются немедленно. Виртуальные машины должны запускать полную ОС, чтобы установить одну программу, для которой требуется процесс полной загрузки. Docker может иметь несколько элементов, и большинство удобств (двоичные файлы и библиотеки) используются совместно с соседями (приложениями). Пока у ВМ нет этих свойств.

---


* [0.5] What are the advantages and disadvantages of using containers over other approaches?

**Преимущества контейнеров**
1. контейнеры работают одинаково в разных средах.
2. контейнеры позволяют планировать различные задачи так, чтобы они выполнялись тогда, когда они необходимы, без вмешательства человека вручную.
3. контейнеры стабильны
4. контейнеры значительно меньше и не требуют больших физических серверов, поскольку они работают исключительно в облаке и используют код приложения и его зависимости.

**Недостатки контейнеров**
1. сопутствующая документация не всегда обновляется так быстро, как сама технология.
2. порог вхождения не-программистов достаточно высок.

---

* [0.5] Explain how Docker works: what are Dockerfiles, how are containers created, and how are they run and destroyed?

**Dockerfile**

Dockerfile — это текстовый документ, содержащий все команды, которые пользователь может вызвать в командной строке для сборки образа.

**Создание Dockerfile:**

    touch Dockerfile
    nano Dockerfile #open the file in text editor

Затем, добавляем следующее:

    FROM ubuntu
    RUN apt update

Сохраняем файл.

**Создаем образ с помощью Dockerfile**

    docker build -t image_name [location of your dockerfile] #create a named image using a Dockerfile 

Вывод должен показать, что образ доступен в репозитории.

**Создаем контейнер**

    docker run [OPTIONS] IMAGE [COMMAND] [ARG...] #run a container

**Удаляем контейнер**

    docker kill [OPTIONS] CONTAINER [CONTAINER...] #destroy one or more running containers

---

* [0.25] Name and describe at least one Docker competitor (i.e., a tool based on the same containerization technology).

**CoreOS Rkt**

Система контейнеров CoreOS rkt позволяет запускать рабочие нагрузки приложений отдельно от базовой инфраструктуры. Это главный конкурент контейнерного движка Docker.

Application Container Image (ACI) из спецификации контейнера приложения служит основой для CoreOS rkt (appc). ACI — это архив, состоящий из манифеста образа и корневой файловой системы со всеми файлами, необходимыми для запуска приложения. Большинство настроек по умолчанию можно изменить во время выполнения, а команда rkt run позволяет пользователям добавлять свои собственные параметры выполнения к определенному образу.

Большинство основных версий ОС Linux поддерживают rkt-контейнеры CoreOS в виде двоичного файла, который интегрируется с системами и сценариями инициализации Linux и работает в соответствии с моделью процессов Unix «родитель-потомок».

---

* [0.25] What is conda? How it differs from apt, yarn, and others?

Conda — это система управления пакетами с открытым исходным кодом и система управления средой, которая работает в Windows, macOS, Linux и z/OS. Conda быстро устанавливает, запускает и обновляет пакеты и их зависимости. Она была создан для программ Python, но может и работать с программным обеспечением любого языка.

Conda более популярен для нужд разработчиков. Это позволяет пользователю работать локально в средах, а не в каталоге root/bin, например, пользователь может использовать разные версии Python в каждой среде (conda vs apt).

## Problem [6.5]

The problem itself is relatively simple. 

Imagine that you developed an excellent RNA-seq analysis pipeline and want to share it with the world. Based on your experience, you are confident that the popularity of the pipeline will be proportional to its ease of use. So, you decided to help your future users and to pack all dependencies in a Conda environment and a Docker container.

Here is the list of tools and their versions that are used in your work:
* [fastqc](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/), v0.11.9
* [STAR](https://github.com/alexdobin/STAR), v2.7.10b
* [reat](https://github.com/alnfedorov/reat), commit ee19bc928badd227975410a6b8b715c0e03bd4ab 
* [samtools](https://github.com/samtools/samtools), v1.16.1
* [picard](https://github.com/broadinstitute/picard), v2.27.5
* [salmon](https://github.com/COMBINE-lab/salmon), commit tag 1.9.0
* [bedtools](https://github.com/arq5x/bedtools2), v2.30.0
* [multiqc](https://github.com/ewels/MultiQC), v1.13


**Anaconda**:

* [1] Install conda, create a new virtual environment, and install all necessary packages. 
* [0.75] You won't be able to install some tools - that's fine. List their names, and explain what should be done to make them conda-friendly ([conda-forge](https://conda-forge.org/docs/maintainer/adding_pkgs.html) channel, [bioconda](https://bioconda.github.io/contributor/workflow.html) channel). 
* [0.25] [Export](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#exporting-the-environment-yml-file) the environment ([example](https://github.com/nf-core/clipseq/blob/master/environment.yml)) to the file and verify that it can be [rebuilt](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-from-an-environment-yml-file) from the file without problems.


**Docker**:
* [3] Create a Dockerfile for a container with **all** required dependencies. Don't forget about comments; test that all tools are accessible and work inside the container. Hints:
 - If needed, grant rights to execute downloaded/compiled binaries using chmod (`chmod a+x BINARY_NAME`)
 - Move all executables to $PATH folders (e.g.`/usr/local/bin`) to make them accessible without specifying the full path.
 - Typical command to run a container interactively (`-it`) and delete on exit(`--rm`): `docker run --rm -it name:tag`
* [1] Use [hadolint](https://hadolint.github.io/hadolint/) and remove as many reported warnings as possible.
* [0.5] Add relevant [labels](https://docs.docker.com/engine/reference/builder/#label), e.g. maintainer, version, etc. ([hint](https://medium.com/@chamilad/lets-make-your-docker-image-better-than-90-of-existing-ones-8b1e5de950d))

###Anaconda

**Устанавливаем anaconda и создаем env**

    bash ~/Anaconda3-2022.10-Linux-x86_64.sh
    conda create --name hometask -y
    conda activate hometask

**Добавляем каналы**

Я добавила канал conda-forge в случае необходимости скачивания из репозиторий GitHub

    conda config --add channels bioconda
    conda config --add channels conda-forge
    
**Устанавливаем пакеты**

Все эти пакеты находятся в https://bioconda.github.io/conda-package_index.html, поэтому им необходимем канал bioconda

    conda install -y samtools=1.16.1
    conda install -y bedtools=2.30.0
    conda install -y multiqc=1.13
    conda install -y fastqc=0.11.9
    conda install -y star=2.7.10b
    conda install -y salmon=1.9.0

Я не нашла необходимую версию Picard, поэтому не стала устанавливать его.

**Экспорт env**
Экспортируем env в env_list.

    conda env export > environment.yml
    conda deactivate
    
    ####Enviroment.yml

```

name: hometask
channels:
  - conda-forge
  - bioconda
dependencies:
  - samtools=1.16.1
  - bedtools=2.30.0
  - multiqc=1.13
  - fastqc=0.11.9 
  - star=2.7.10b
  - salmon=1.9.0

prefix: /home/karina/anaconda3/envs/hometask
```
###Docker

### Install Docker
```
sudo apt update
sudo apt upgrade
sudo apt install docker.io
sudo snap install docker
```

Some useful links:
1.  https://gist.github.com/alyleite/ca8b10581dbecd722d9dcc35b50d9b2b
2.  https://www.simplilearn.com/tutorials/docker-tutorial/how-to-install-docker-on-ubuntu

    touch Dockerfile
    nano Dockerfile

####Dockerfile

```
FROM ubuntu:latest
MAINTAINER "karina"

#update links and install apt-utils apt-transport-https unzip and python3-pip
RUN apt-get update
RUN apt -y install apt-utils
RUN apt -y install apt-transport-https
RUN apt -y install openjdk-11-jre-headless
RUN apt -y install unzip
RUN apt -y install python3-pip

#create /.bashrc for aliases
RUN touch /.bashrc

#run samtools v1.16.1
RUN wget https://github.com/samtools/samtools/archive/refs/tags/1.16.1.zip -O ./samtools-1.16.1.zip && \
    unzip samtools-1.16.1.zip && \
    rm samtools-1.16.1.zip && \
    mv samtools-1.16.1/misc samtools && \
    rm -r samtools-1.16.1 && \
    echo 'alias samtools="/samtools/samtools.pl"' >> /.bashrc

#run bedtools v.2.30.0
RUN wget https://github.com/arq5x/bedtools2/releases/download/v2.30.0/bedtools.static.binary -O /bin/bedtools.static.binary && \
    chmod a+x /bin/bedtools.static.binary && \
    echo 'alias bedtools="/bin/bedtools.static.binary"' >> /.bashrc

#run multic v1.13
RUN pip install multiqc==1.13

#run fastqc v0.11.9
RUN wget https://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.11.9.zip && \
    unzip fastqc_v0.11.9.zip && \
    rm fastqc_v0.11.9.zip && \
    chmod a+x FastQC/fastqc && \
    echo 'alias fastqc="/FastQC/fastqc"' >> /.bashrc

#run STAR v.2.7.10b
RUN wget https://github.com/alexdobin/STAR/releases/download/2.7.10b/STAR_2.7.10b.zip && \
    unzip STAR_2.7.10b.zip && \
    rm STAR_2.7.10b.zip && \
    chmod a+x STAR_2.7.10b/Linux_x86_64_static/STAR && \
    mv STAR_2.7.10b/Linux_x86_64_static/STAR /bin/STAR && \
    rm -r STAR_2.7.10b

#run salmon v.1.9.0
RUN wget https://github.com/COMBINE-lab/salmon/releases/download/v1.9.0/salmon-1.9.0_linux_x86_64.tar.gz && \
    tar -zxvf salmon-1.9.0_linux_x86_64.tar.gz && \
    rm salmon-1.9.0_linux_x86_64.tar.gz && \
    chmod a+x salmon-1.9.0_linux_x86_64/bin/salmon && \
    mv salmon-1.9.0_linux_x86_64/bin/salmon /bin/salmon && \
    rm -r salmon-1.9.0_linux_x86_64 
```
**Создаем docker образ**. Запускаем container и проверяем, что он удалится после команды **exit**.
```
 sudo docker build -t ubuntu:latest
 sudo docker run --rm -it ubuntu:latest

```
**Изменение Dockerfile**

Я использовала Linter и удалила весь отчет в данном порядке:
1.   MAINTAINER --> LABEL maintainer
2.   Удалить the apt-get lists после установки чего-либо
3.   apt --> apt-get install package="package_vesrion"
4.   latest --> 20.04
5.   Уменьшить количество шагов

Также я добавила несколько LABEL для имени и описания.

####Linter Labeled Optimazed Dockerfile
```
FROM ubuntu:20.04
LABEL maintainer="karina"

LABEL org.label-schema.name="ht1"
LABEL org.label-schema.description="Hometask 1 Karina Burmak"

#apt-transport-https, python-pip, and unzip
RUN apt-get update && \
  apt-get install -y install apt-utils=2.4.8 && \
  apt-get install -y apt-transport-https=2.0.2ubuntu0.2 && \
  apt-get -y install openjdk-11-jre-headless=11.0.17+8-1ubuntu2~20.04 && \
  apt-get -y install python3-pip=20.0.2-5ubuntu1.5 && \
  apt-get -y install unzip=6.0-25ubuntu1.1 && \ 
  apt-get clean && \ 
  rm -rf /var/lib/apt/lists/*

#create /.bashrc for aliases
RUN touch /.bashrc

#run samtools v1.16.1
RUN wget https://github.com/samtools/samtools/archive/refs/tags/1.16.1.zip -O ./samtools-1.16.1.zip && \
    unzip samtools-1.16.1.zip && \
    rm samtools-1.16.1.zip && \
    mv samtools-1.16.1/misc samtools && \
    rm -r samtools-1.16.1 && \
    echo 'alias samtools="/samtools/samtools.pl"' >> /.bashrc

#run bedtools v.2.30.0
RUN wget https://github.com/arq5x/bedtools2/releases/download/v2.30.0/bedtools.static.binary -O /bin/bedtools.static.binary && \
    chmod a+x /bin/bedtools.static.binary && \
    echo 'alias bedtools="/bin/bedtools.static.binary"' >> /.bashrc

#run multic v1.13
RUN pip install multiqc==1.13

#run fastqc v0.11.9
RUN wget https://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.11.9.zip && \
    unzip fastqc_v0.11.9.zip && \
    rm fastqc_v0.11.9.zip && \
    chmod a+x FastQC/fastqc && \
    echo 'alias fastqc="/FastQC/fastqc"' >> /.bashrc

#run STAR v.2.7.10b
RUN wget https://github.com/alexdobin/STAR/releases/download/2.7.10b/STAR_2.7.10b.zip && \
    unzip STAR_2.7.10b.zip && \
    rm STAR_2.7.10b.zip && \
    chmod a+x STAR_2.7.10b/Linux_x86_64_static/STAR && \
    mv STAR_2.7.10b/Linux_x86_64_static/STAR /bin/STAR && \
    rm -r STAR_2.7.10b

#run salmon v.1.9.0
RUN wget https://github.com/COMBINE-lab/salmon/releases/download/v1.9.0/salmon-1.9.0_linux_x86_64.tar.gz && \
    tar -zxvf salmon-1.9.0_linux_x86_64.tar.gz && \
    rm salmon-1.9.0_linux_x86_64.tar.gz && \
    chmod a+x salmon-1.9.0_linux_x86_64/bin/salmon && \
    mv salmon-1.9.0_linux_x86_64/bin/salmon /bin/salmon && \
    rm -r salmon-1.9.0_linux_x86_64 

```
## Extra points [1.5]

You will be awarded extra points for the following:
* [0.5] Using [multi-stage builds](https://docs.docker.com/build/building/multi-stage/) in Docker. E.g. to build reat and copy only the executable to the final image.

* [0.75] Minimizing the size of the final Docker image. That is, removing all intermediates, unnecessary binaries/caches, etc. Don't forget to compare & report the final size before and after all the optimizations.

* [0.25] Create an extra Dockerfile that starts from [a conda base image](https://hub.docker.com/r/continuumio/anaconda3) and builds everything from your conda environment file. 

Hint: `conda env create --quiet -f environment.yml && conda clean -a` ([example](https://github.com/nf-core/clipseq/blob/master/Dockerfile))

Docker образ:
*   via raw Dockerfile - **2.01 Gb**
*   via optimized Dockerfile - **1.32 Gb**


###Dockerfile for conda


```
FROM continuumio/anaconda3
LABEL maintainer="karina"
#labels   
LABEL org.label-schema.name="ht1"
LABEL org.label-schema.description="Hometask 1 Karina Burmak"

#create the env    
COPY environment.yml . 
RUN apt-get update && apt-get -y install apt-utils=2.4.8 && \
  apt-get -y installed apt-transport-https=2.0.2ubuntu0.2 && \
  conda env create --file environment.yml && conda clean -a  
```
Docker — это действительно удобно и полезно.
