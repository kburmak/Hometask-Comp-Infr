# 2 - Working with remote servers

**git branch name:** jbrowser

## Theory [2]

* [0.4] What are [computer ports](https://www.cloudflare.com/learning/network-layer/what-is-a-computer-port/) on a high level? How many ports are there on a typical computer?
A real-life situation that occurred to me several times over the years.

Imagine wrapping up a large bioinformatics project and wanting to share raw data with your colleagues in a friendly and straightforward format. The best option would be to use an online genome browser and host your data remotely, so it is easily accessible by anyone with a valid link. This is exactly what we will be doing here.

*Please consider doing this HW using Linux since setting up the SSH client on Windows is painful, and I won't be able to help you.*

**Компьютерные порты** — это программные уникальные идентификаторы, помогающие ОС разделять потоки информации. Порт как дверь, а у ОС их несколько. Он отправляет и получает различные виды информации через разные порты. Когда пакет информации отправляется с определенного порта компьютера, он содержит информацию о порте, через который он должен быть получен на целевом компьютере. И целевой компьютер «слушает» определенную дверь, чтобы получить такую информацию.

Порт — целое число от 0 до 65535, но для TCP нулевой порт зарезервирован и не может быть использован, а в UDP означает отсутствие порта. Не все порты используются в обычном компьютере. Интенсивно используются и широко известны около 18 портов:

**Port number  ->  Protocol used**

    20 -> File Transfer Protocol (FTP) Data Transfer
    21 -> File Transfer Protocol (FTP) Command Control
    22 -> Secure Shell (SSH) Secure Login
    23 -> Telnet remote login service, unencrypted text messages
    25 -> Simple Mail Transfer Protocol (SMTP) email delivery
    53 -> Domain Name System (DNS) service
    67, 68 -> Dynamic Host Configuration Protocol (DHCP)
    80 -> Hypertext Transfer Protocol (HTTP) used in the World Wide Web
    110 -> Post Office Protocol (POP3)
    119 -> Network News Transfer Protocol (NNTP)
    123 -> Network Time Protocol (NTP)
    143 -> Internet Message Access Protocol (IMAP) Management of digital mail
    161 -> Simple Network Management Protocol (SNMP)
    194 -> Internet Relay Chat (IRC)
    443 -> HTTP Secure (HTTPS) HTTP over TLS/SSL
    546, 547 -> DHCPv6 IPv6 version of DHCP

* [0.4] What is the difference between http, https, ssh, and other protocols? In what sense are they similar? Name default ports for several data transfer protocols.
Все клиент-серверные протоколы определяют семантику клиент-серверных взаимодействий. Они работают на уровне приложений. Когда данные передаются по протоколу, это означает, что к данным добавляются специфические для протокола блоки информации, которые должны быть распознаны участником сети. Блоки, специфичные для протокола, содержат некоторую метаинформацию о версии протокола, дате, времени, статусе и т. д. Содержимое блока — это то, что отличает один протокол от другого. Еще одно отличие — шифрование при передаче протокола. HTTP, FTP не имеют шифрования, в то время как HTTPS, SSH или SFTP имеют. Они используются для разных задач. Грубо говоря, HTML-страницы передаются по протоколам HTTP/HTTPS, а SSH, FTP, FTPS, SFTP обеспечивают удаленную работу в файловых системах.

Самые известные порты: 80 для http, 443 для https и 20/21 для FTP.

* [0.4] Explain briefly: (1) what is IP, (2) what IPs are called 'white'/public, (3) and what happens when you enter 'google.com' into the web browser. 

**IP** — уникальный идентификатор устройства в глобальной или локальной компьютерной сети. Он работает с помощью протокола IP на аппаратном сетевом уровне. Он имеет две версии:

*   IPv4 выглядит так: `192.0.2.235`;
*   IPv6 выглядит так: `2001:0db8:11a3:09d7:1f34:8a2e:07a0:765d`.

**Общедоступный IP**-адрес — это IP-адрес, доступный пользователям Интернета, в отличие от **частного IP**-адреса, принадлежащего локальной сети. Несколько IP-номеров зарезервированы для использования в локальных сетях.

При выполнении запроса браузера, например, «HSE», URL-адрес переходит на ближайший доступный DNS-сервер. Там запрос разрешается на IP-адрес веб-сайта «Вышки», и его IP-адрес отправляется обратно в мой браузер. После этого мой браузер делает запрос по полученному IP в интернет. Веб-сервер ВШЭ получает мой запрос и отвечает HTML-страницей.

* [0.4] What is Nginx? How does it work on the high level? List several alternative web servers.

**Nginx** — самый распространенный веб-сервер. Он использует порты 80 (для `http`) и 443 (для `https`), обрабатывает запросы `http/https` и отвечает на эти запросы или проксирует их на другие серверы или другие порты того же сервера. Это программный сервер, который должен быть настроен на аппаратном сервере (или обычном компьютере) для правильной работы.

**Apache** — одна из самых известных альтернатив nginx, но есть также LiteSpeed Web Server, Microsoft-IIS и т.д.

* [0.4] What is SSH, and for what is it typically used? Explain two ways to authenticate in an SSH server in detail.

**SSH** — это сетевой протокол, позволяющий удаленно управлять операционной системой. Это уровень приложений, и он использует шифрование во время передачи информации.

There are two ways to authenticate in an SSH server.

1. Аутентификация по паролю. В этом случае мы отправляем запрос на SSH-сервер для подключения. В этот момент уникальный отпечаток создается и сохраняется на нашем компьютере. Это выглядит так: `12:f8:7e:78:61:b4:bf:e2:de:24:15:96:4e:d4:72:53`. Отпечаток помогает идентифицировать наше особое соединение клиент-сервер. Это помогает предотвратить подключение компьютера с таким же IP-адресом к SSH-серверу, поскольку такой компьютер не имеет отпечатка пальца. SSH-сервер имеет базу данных с IP-адресами клиентов и их паролями. Когда соединение инициируется, сервер SSH требует ввести пароль. Когда пароль введен и сервер проверил его, начинается соединение.

2. Аутентификация по ключевой паре. Генерируются **закрытый и открытый ключи**. Закрытый ключ хранится на клиентском компьютере и не должен публиковаться. Открытый ключ открыт для всех и не имеет смысла без закрытого. Собственно, публичный ключ — это что-то вроде замка. Его видят все, но открыть его может только владелец закрытого ключа. Для запуска соединения клиент отправляет свой открытый ключ на SSH-сервер для распознавания клиента. Отпечаток пальца создается, как указано в разделе «Аутентификация по паролю». Если сервер находит совпадение открытого ключа, он отправляет информацию, зашифрованную с помощью открытого ключа. Он не может расшифровать информацию без закрытого ключа. Он просто отправляет зашифрованную информацию клиенту, который расшифровывает ее с помощью своего закрытого ключа.

## Problem [6.5]

teps:
* [2] Create a new virtual machine in the Yandex/Mail/etc cloud (order at least 10GB of free disk space). Generate SSH key pair and use it to connect to your server.
* [1] Download the latest human genome assembly (GRCh38) from the Ensemble FTP server ([fasta](https://ftp.ensembl.org/pub/release-108/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz), [GFF3](https://ftp.ensembl.org/pub/release-108/gff3/homo_sapiens/Homo_sapiens.GRCh38.108.gff3.gz)). Index the fasta using samtools (`samtools faidx`) and GFF3 using tabix. 

* [1] Select and download BED files for three ChIP-seq and one ATAC-seq experiment from the ENCODE (use one tissue/cell line). Sort, bgzip, and index them using tabix.

* [1] Download and install [JBrowse 2](https://jbrowse.org/jb2/). Create a new jbrowse [repository](https://jbrowse.org/jb2/docs/cli/#jbrowse-create-localpath) in `/mnt/JBrowse/` (or some other folder).
* [0.25] Install nginx and amend its config(/etc/nginx/nginx.conf) to contain the following section:
```
http {
  # Don't touch other options!
  # ........
  # ........

  # Add this:
  server {
		listen 80;
		index index.html;

		location /jbrowse/ {
			alias /mnt/JBrowse/;	
		}
	}

  # ........
}
```
* [0.25] Restart the nginx (reload its config) and make sure that you can access the browser using a link like this: `http://64.129.58.13/jbrowse/`. Here `64.129.58.13` is your public IP address.
* [1] Add your files to the genome browser and verify that everything works as intended. Don't forget to [index](https://jbrowse.org/jb2/docs/cli/#jbrowse-text-index) the genome annotation, so you could later search by gene names.


**Remember to put a persistent link to a session with all your BED files and the genome annotation in the report. I must be able to access it without problems.**

### Creating a VM
**Создание виртуальной машины**

Я создала ВМ с помощью Yandex Cloud.
```
ssh-keygen -t ed25519
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOIQwhGB8oke5Sc/raXsjAUYAF4x62WodlcN+vaNnwd8 pavel@karina-ms-7a38
```

Параметры:

Параметры:

Платформа - Intel Ice Lake
vCPU - 2 Gb
RAM - 4  Gb

Устанавливаем необходимые пакеты:

```
ssh -i id_ed25519 kburmak@158.160.40.98
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install -y samtools
sudo apt-get install -y tabix
```
### Human genome assembly

**Загружаем последнюю сборку человеческого генома в форматах `fasta` и `gff3`**

```
wget https://ftp.ensembl.org/pub/release-108/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz
gunzip Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz
samtools faidx Homo_sapiens.GRCh38.dna.primary_assembly.fa #genome index

wget https://ftp.ensembl.org/pub/release-108/gff3/homo_sapiens/Homo_sapiens.GRCh38.108.gff3.gz
gunzip Homo_sapiens.GRCh38.108.gff3.gz
(grep "^#" Homo_sapiens.GRCh38.108.gff3; grep -v "^#" Homo_sapiens.GRCh38.108.gff3 | sort -t"`printf '\t'`" -k1,1 -k4,4n) | bgzip > sorted.Homo_sapiens.GRCh38.108.gff3.gz;
tabix -p gff sorted.Homo_sapiens.GRCh38.108.gff3.gz;
```
### ChIP-seqи, сухареки

**Загружаем BED и ATAC-seq файлы. Применяем к ним функции `sort`, `bgzip` и `index` к ним, используя `tabix`**

```
wget -nc https://www.encodeproject.org/files/ENCFF664LSW/@@download/ENCFF664LSW.bed.gz  -O chipseq1.bed.gz

wget -nc https://www.encodeproject.org/files/ENCFF276WTW/@@download/ENCFF276WTW.bed.gz -O chipseq2.bed.gz

wget -nc https://www.encodeproject.org/files/ENCFF666WMP/@@download/ENCFF666WMP.bed.gz -O chipseq3.bed.gz

wget -nc https://www.encodeproject.org/files/ENCFF031OEA/@@download/ENCFF031OEA.bed.gz -O atacseq.bed.gz

for a in atacseq chipseq1 chipseq2 chipseq3;
do
    gunzip ${a}.bed.gz
    bedtools sort -i $a.bed > sort_${a}.bed
    bgzip sort_${a}.bed
    tabix sort_${a}.bed.gz
done
```
### JBrowse 2 and Nginx

**Устанавливаем JBrowse 2**

```
sudo apt install npm
sudo npm install -g @jbrowse/cli

# And for path from your source
jbrowse create JBrowse
```

**Устанавливаем Nginx** 
```
sudo apt-get install -y nginx
cd /etc/nginx
sudo nano nginx.conf
```

```
  #include /etc/nginx/sites-enabled/*
    
  server {
    listen 80 default_server;
    index index.html;
    server_name _;

    # Don't put JBrowse inside the home directory!
    # You will have problems with permissions
    location /jbrowse/ {
      alias /home/kburmak/JBrowse/;	
    }
  }
```

```
sudo nginx -s reload
http://158.160.40.98/jbrowse/
```

```
sudo jbrowse add-assembly Homo_sapiens.GRCh38.dna.primary_assembly.fa --load copy --out /home/kburmak/JBrowse/

for i in chipseq1 chipseq2 chipseq3 atacseq;
do
  gunzip sort_${i}.bed.gz
  awk '{gsub(/^chr/,""); print}' sort_${i}.bed > $(echo sort_${i}.bed| cut -d '.' -f 1)'_renamed.bed'
  bgzip sort_${i}_renamed.bed
  tabix -f sort_${i}_renamed.bed.gz
done

sudo jbrowse add-track sorted.Homo_sapiens.GRCh38.108.gff3.gz  --load copy --out /home/kburmak/JBrowse/

for i in chipseq1 chipseq2 chipseq atacseq;
do 
  sudo jbrowse add-track sort_${i}_renamed.bed.gz --load copy --out /home/kburmak/JBrowse/
done
```
## Extra points [1.5]

* [1] Create a Docker container for running JBrowse 2. It should be a self-contained application, listening on the default HTTP port. Users must be able to mount directories with custom configs and access them later without any problems. 

Hint: to specify the config, use the config=PATH query parameter. E.g. `http://64.129.58.13/jbrowse/?config=my_folder%2Fconfig.json` where `my_folder%2Fconfig.json` is the [escaped](https://en.wikipedia.org/wiki/Percent-encoding) path to the config file.



* [0.5] Give an in-depth explanation of the OSI model and how the TCP/IP stack works. Don't copy-paste descriptions from the internet; paraphrase and shorten as much as possible (imagine writing a cheat sheet for yourself).

**Open Systems Interconnections (OSI)** - логическая и концептуальная модель, определяющая сетевое взаимодействие, используемое системами, открытыми для взаимодействия и связи с другими системами.

*Характеристики модели OSI:*

*   Слой следует создавать только там, где необходимы определенные уровни абстракции;
*   Функция каждого уровня должна быть выбрана в соответствии с международными стандартизированными протоколами;
*   Количество слоев должно быть большим, чтобы отдельные функции не помещались в один и тот же слой. В то же время он должен быть достаточно небольшим, чтобы архитектура не сильно усложнялась;
*   В модели OSI каждый уровень опирается на следующий более низкий уровень для выполнения примитивных функций, соответственно, каждый уровень должен иметь возможность предоставлять услуги следующему более высокому уровню;
*   Изменения, сделанные в одном слое, не должны требовать изменений в других слоях.

У модели OSI есть *7 уровней*:
1. **Физический уровень**. Основной функцией этого уровня является преобразование потока битов в физическую среду путем преобразования его в электрические/оптические импульсы или радиосигналы. Этот уровень обеспечивает физическое соединение с базовой средой, а также предоставляет аппаратные средства для активации, поддержания и деактивации физических соединений между объектами канала передачи данных. Это включает в себя упорядочивание потока битов, идентификацию каналов на базовом носителе и, возможно, мультиплексирование. Это не следует путать с самой фактической средой.
2. **Уровень канала передачи данных**. Канальный уровень действует как драйвер физического уровня и контролирует его работу. Канальный уровень отправляет данные на физический уровень на передающей стороне и принимает данные с физического уровня на приемном узле. Он также обеспечивает обнаружение и исправление ошибок, которые могли возникнуть во время передачи/приема на физическом носителе, а также определяет процесс управления потоком между двумя узлами, чтобы избежать переполнения буфера на любой стороне соединения канала передачи данных. 
3. **Сетевой уровень**. Этот уровень отвечает за поиск правильных промежуточных узлов, которые могут потребоваться для отправки данных узлу назначения, если узел назначения не находится в той же сети, что и исходный узел. Этот уровень также разбивает дейтаграммы на более мелкие фрагменты, если нижележащий уровень канала передачи данных не способен обработать дейтаграмму, предлагаемую сетевому уровню для передачи по сети.
4. **Транспортный уровень**. Транспортный уровень обеспечивает функциональные и процедурные средства передачи последовательностей данных переменной длины от источника к узлу назначения через одну или несколько сетей. Этот уровень имеет сквозное значение и предоставляет услуги без установления или с установлением соединения для сеансового уровня. Этот уровень отвечает за установление соединения, управление и освобождение.
5. **Сеансовый уровень**. Основная цель этого уровня — координировать и синхронизировать диалог между уровнями представления в двух конечных точках и управлять их обменом данными. Этот уровень устанавливает, управляет и завершает соединения между приложениями. Сеансовый уровень устанавливает, координирует и завершает диалоги, обмены и диалоги между приложениями на каждом конце.
6. **Уровень представления**. Уровень представления обеспечивает общее представление данных, передаваемых между объектами приложения, и обеспечивает независимость от различий в представлении/синтаксисе данных. Этот уровень также иногда называют уровнем синтаксиса. Уровень представления работает для преобразования данных в форму, которую может принять прикладной уровень. Этот уровень также отвечает за шифрование и дешифрование данных приложения.
7. **Прикладной уровень**. Прикладной уровень является самым верхним уровнем модели OSI и не имеет протоколов верхнего уровня. Программные приложения, которым требуется связь с другими системами, напрямую взаимодействуют с прикладным уровнем OSI. Этот уровень не следует путать с прикладным программным обеспечением, которое представляет собой программу, реализующую программное обеспечение; например, `HTTP` — это протокол прикладного уровня, а Google Chrome — это программное приложение.

**TCP/IP** - протокол управления передачей/интернет-протокол. Эта модель помогает определить, как конкретный компьютер должен быть подключен к Интернету и как можно передавать данные между ними.

*Характеристики модели OSI:*

*   Поддержка гибкой архитектуры;
*   Добавить больше систем в сеть н- простая задача;
*   В TCP/IP сеть остается нетронутой до тех пор, пока исходная и конечная машины не будут работать должным образом;
*   TCP — это протокол, ориентированный на соединение;
*   TCP обеспечивает надежность и гарантирует, что данные, поступающие не по порядку, должны быть упорядочены;
*   TCP позволяет реализовать управление потоком, поэтому отправитель никогда не перегружает получателя данными.

У модели TCP/IP есть *4 уровня*:
1. **Прикладной уровень**. На этом уровне работает большинство сетевых приложений.
2. **Транспортный уровень**. Протоколы транспортного уровня могут решать проблему негарантированной доставки сообщений («дошло ли сообщение до адресата?»), а также гарантировать правильную последовательность прихода данных. В стеке TCP/IP транспортные протоколы определяют, для какого именно приложения предназначены эти данные.
3. **Межсетевой уровень**. Межсетевой уровень (Network layer) изначально разработан для передачи данных из одной сети в другую. На этом уровне работают маршрутизаторы, которые перенаправляют пакеты в нужную сеть путём расчёта адреса сети по маске сети. Пакеты сетевого протокола IP могут содержать код, указывающий, какой именно протокол следующего уровня нужно использовать, чтобы извлечь данные из пакета. Это число — уникальный IP-номер протокола.
4. **Канальный уровень**. Канальный уровень описывает способ кодирования данных для передачи пакета данных на физическом уровне. Ethernet, например, в полях заголовка пакета содержит указание того, какой машине или машинам в сети предназначен этот пакет.

