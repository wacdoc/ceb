# Paghimo sa imong kaugalingon nga SMTP mail nga nagpadala server

## pasiuna

Ang SMTP mahimong direktang makapalit og mga serbisyo gikan sa mga cloud vendor, sama sa:

* [Amazon SES SMTP](https://docs.aws.amazon.com/ses/latest/dg/send-email-smtp.html)
* [Ali cloud email push](https://www.alibabacloud.com/help/directmail/latest/three-mail-sending-methods)

Mahimo ka usab nga maghimo sa imong kaugalingon nga mail server - walay kinutuban nga pagpadala, ubos nga kinatibuk-ang gasto.

Sa ubos, gipakita namon ang lakang sa lakang kung giunsa paghimo ang among kaugalingon nga mail server.

## Pagpili sa server

Ang self-host nga SMTP server nanginahanglan usa ka publiko nga IP nga adunay mga port nga 25, 456, ug 587 nga bukas.

Ang kasagarang gigamit nga mga panganod sa publiko nag-ali niini nga mga pantalan pinaagi sa default, ug mahimo’g posible nga ablihan kini pinaagi sa pag-isyu sa usa ka mando sa trabaho, apan kini labi ka hasol.

Girekomenda ko ang pagpalit gikan sa usa ka host nga adunay bukas nga mga pantalan ug nagsuporta sa pag-set up sa mga reverse domain name.

Dinhi, girekomenda ko [ang Contabo](https://contabo.com) .

Ang Contabo usa ka host provider nga nakabase sa Munich, Germany, nga gitukod kaniadtong 2003 nga adunay kaayo nga kompetisyon nga mga presyo.

Kung imong pilion ang Euro isip currency sa pagpalit, ang presyo mahimong mas barato (usa ka server nga adunay 8GB nga memorya ug 4 nga mga CPU nagkantidad og mga 529 yuan kada tuig, ug ang inisyal nga bayad sa pag-instalar libre sa usa ka tuig).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/UoAQkwY.webp)

Kung nagbutang usa ka order, isulti `prefer AMD` , ug ang server nga adunay AMD CPU adunay mas maayo nga performance.

Sa mosunod, akong kuhaon ang Contabo's VPS isip usa ka ehemplo aron ipakita kung unsaon paghimo sa imong kaugalingong mail server.

## Pag-configure sa sistema sa Ubuntu

Ang operating system dinhi mao ang Ubuntu 22.04

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/smpIu1F.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/m7Mwjwr.webp)

Kung ang server sa ssh nagpakita `Welcome to TinyCore 13!` (sama sa gipakita sa hulagway sa ubos), kini nagpasabot nga ang sistema wala pa ma-install. Palihug idiskonekta ang ssh ug paghulat sa pipila ka minuto aron maka-log in pag-usab.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/-qKACz9.webp)

Kung makita `Welcome to Ubuntu 22.04.1 LTS` , kompleto na ang pagsugod, ug mahimo nimong ipadayon ang mosunod nga mga lakang.

### [Opsyonal] I-initialize ang development environment

Kini nga lakang kay opsyonal.

Para sa kasayon, akong gibutang ang instalasyon ug sistema sa configuration sa ubuntu software sa [github.com/wactax/ops.os/tree/main/ubuntu](https://github.com/wactax/ops.os/tree/main/ubuntu) .

Pagdalagan ang mosunod nga sugo aron ma-install sa usa ka pag-klik.

```
bash <(curl -s https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

Intsik nga tiggamit, palihog gamita ang mosunod nga sugo sa baylo, ug ang pinulongan, time zone, ug uban pa awtomatikong itakda.

```
CN=1 bash <(curl -s https://ghproxy.com/https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

### Gitugotan sa Contabo ang IPV6

I-enable ang IPV6 aron ang SMTP makapadala usab og mga email nga adunay mga adres sa IPV6.

usba `/etc/sysctl.conf`

Usba o idugang ang mosunod nga mga linya

```
net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.default.disable_ipv6 = 0
net.ipv6.conf.lo.disable_ipv6 = 0
```

Pag-follow up [sa contabo tutorial: Pagdugang sa IPv6 connectivity sa imong server](https://contabo.com/blog/adding-ipv6-connectivity-to-your-server/)

I-edit `/etc/netplan/01-netcfg.yaml` , pagdugang og pipila ka linya sama sa gipakita sa hulagway sa ubos (Contabo VPS default configuration file aduna na niini nga mga linya, i-uncomment lang kini).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/5MEi41I.webp)

Unya `netplan apply` aron matuman ang giusab nga configuration.

Human magmalampuson ang configuration, mahimo nimong gamiton `curl 6.ipw.cn` aron makita ang ipv6 address sa imong external network.

## I-clone ang configuration repository ops

```
git clone https://github.com/wactax/ops.soft.git
```

## Paghimo og libre nga SSL certificate para sa imong domain name

Ang pagpadala sa mail nanginahanglan usa ka sertipiko sa SSL alang sa pag-encrypt ug pagpirma.

Gigamit namo [ang acme.sh](https://github.com/acmesh-official/acme.sh) aron makamugna og mga sertipiko.

Ang acme.sh usa ka bukas nga gigikanan nga awtomatiko nga himan sa pagpirma sa sertipiko,

Pagsulod sa configuration warehouse ops.soft, run `./ssl.sh` , ug usa ka `conf` folder ang pagabuhaton sa **ibabaw nga direktoryo** .

Pangitaa ang imong DNS provider gikan sa [acme.sh dnsapi](https://github.com/acmesh-official/acme.sh/wiki/dnsapi) , edit `conf/conf.sh` .

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Qjq1C1i.webp)

Dayon padagana `./ssl.sh 123.com` aron makamugna og `123.com` ug `*.123.com` nga mga sertipiko para sa imong domain name.

Ang una nga pagdagan awtomatiko nga mag-install [sa acme.sh](https://github.com/acmesh-official/acme.sh) ug magdugang usa ka naka-iskedyul nga buluhaton alang sa awtomatik nga pagbag-o. Makita nimo `crontab -l` , adunay ingon nga linya sama sa mosunod.

```
52 0 * * * "/mnt/www/.acme.sh"/acme.sh --cron --home "/mnt/www/.acme.sh" > /dev/null
```

Ang agianan alang sa nahimo nga sertipiko sama sa `/mnt/www/.acme.sh/123.com_ecc。`

Ang pagbag-o sa sertipiko motawag sa `conf/reload/123.com.sh` nga script, usba kini nga script, mahimo nimong idugang ang mga mando sama sa `nginx -s reload` aron ma-refresh ang cache sa sertipiko sa mga may kalabutan nga aplikasyon.

## Paghimo SMTP server nga adunay chasquid

[Ang chasquid](https://github.com/albertito/chasquid) usa ka open source SMTP server nga gisulat sa Go language.

Isip kapuli sa karaang mga programa sa mail server sama sa Postfix ug Sendmail, ang chasquid mas simple ug mas sayon ​​gamiton, ug mas sayon ​​usab kini alang sa sekondaryang kalamboan.

Run `./chasquid/init.sh 123.com` awtomatik nga ma-install sa usa ka pag-klik (ilisan ang 123.com sa imong nagpadala nga domain name).

## I-configure ang Email Signature DKIM

Ang DKIM gigamit sa pagpadala sa mga pirma sa email aron mapugngan ang mga sulat nga isipon nga spam.

Human nga malampuson nga modagan ang sugo, aghaton ka sa pagtakda sa DKIM record (sama sa gipakita sa ubos).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/LJWGsmI.webp)

Pagdugang lang ug TXT record sa imong DNS (sama sa gipakita sa ubos).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/0szKWqV.webp)

## Tan-awa ang kahimtang sa serbisyo ug mga log

 `systemctl status chasquid` Tan-awa ang status sa serbisyo.

Ang kahimtang sa normal nga operasyon sama sa gipakita sa numero sa ubos

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/CAwyY4E.webp)

 `grep chasquid /var/log/syslog` o `journalctl -xeu chasquid` makakita sa error log.

## Reverse domain name configuration

Ang reverse domain name mao ang pagtugot sa IP address nga masulbad sa katugbang nga domain name.

Ang pagbutang ug reverse domain name makapugong sa mga email nga mailhan nga spam.

Kung madawat na ang mail, ang tigdawat nga server mohimo ug reverse domain name analysis sa IP address sa nagpadala nga server aron makumpirma kung ang nagpadala nga server adunay balido nga reverse domain name.

Kung ang nagpadala nga server walay reverse domain name o kung ang reverse domain name dili motakdo sa IP address sa nagpadala nga server, ang tigdawat nga server mahimong makaila sa email isip spam o mosalikway niini.

Bisitaha ang [https://my.contabo.com/rdns](https://my.contabo.com/rdns) ug i-configure sama sa gipakita sa ubos

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/IIPdBk_.webp)

Human mabutang ang reverse domain name, hinumdumi nga i-configure ang forward resolution sa domain name nga ipv4 ug ipv6 sa server.

## I-edit ang hostname sa chasquid.conf

Usba `conf/chasquid/chasquid.conf` sa bili sa reverse domain name.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/6Fw4SQi.webp)

Dayon padagana ang `systemctl restart chasquid` aron ma-restart ang serbisyo.

## I-backup ang conf sa git repository

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Fier9uv.webp)

Pananglitan, akong gi-back up ang conf folder sa akong kaugalingong proseso sa github sama sa mosunod

Paghimo una og pribadong bodega

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ZSCT1t1.webp)

Pagsulod sa direktoryo sa conf ug isumite sa bodega

```
git init
git add .
git commit -m "init"
git branch -M main
git remote add origin git@github.com:wac-tax-key/conf.git
git push -u origin main
```

## Idugang ang nagpadala

dagan

```
chasquid-util user-add i@wac.tax
```

Makadugang sender

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/khHjLof.webp)

### Tinoa nga ang password gitakda sa husto

```
chasquid-util authenticate i@wac.tax --password=xxxxxxx
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/e92JHXq.webp)

Human madugang ang user, `chasquid/domains/wac.tax/users` ma-update, hinumdomi nga isumite kini sa bodega.

## DNS idugang ang SPF record

Ang SPF (Sender Policy Framework) usa ka teknolohiya sa pag-verify sa email nga gigamit aron mapugngan ang pagpanglimbong sa email.

Gipamatud-an niini ang identidad sa usa ka nagpadala sa mail pinaagi sa pagsusi nga ang IP address sa nagpadala nagtugma sa mga rekord sa DNS sa ngalan sa domain nga giangkon niini, nga nagpugong sa mga mangingilad sa pagpadala ug mga peke nga email.

Ang pagdugang sa mga rekord sa SPF makapugong sa mga email nga mailhan nga spam kutob sa mahimo.

Kung ang imong domain name server dili mosuporta sa SPF type, idugang lang ang TXT type record.

Pananglitan, ang SPF sa `wac.tax` mao ang mosunod

`v=spf1 a mx include:_spf.wac.tax include:_spf.google.com ~all`

SPF para sa `_spf.wac.tax`

`v=spf1 a:smtp.wac.tax ~all`

Timan-i nga ako adunay `include:_spf.google.com` dinhi, kini tungod kay akong i-configure `i@wac.tax` ingon nga nagpadala nga adres sa Google mailbox sa ulahi.

## DNS configuration DMARC

Ang DMARC mao ang minubo sa (Domain-based Message Authentication, Reporting & Conformance).

Gigamit kini aron makuha ang mga pag-bounce sa SPF (tingali tungod sa mga sayup sa pag-configure, o adunay laing tawo nga nagpakaaron-ingnon nga ikaw magpadala sa spam).

Idugang ang TXT record `_dmarc` ,

Ang sulod mao ang mosunod

```
v=DMARC1; p=quarantine; fo=1; ruf=mailto:ruf@wac.tax; rua=mailto:rua@wac.tax
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/k44P7O3.webp)

Ang kahulogan sa matag parameter mao ang mosunod

### p (Patakaran)

Nagpakita kung giunsa pagdumala ang mga email nga napakyas sa pag-verify sa SPF (Sender Policy Framework) o DKIM (DomainKeys Identified Mail). Ang p parameter mahimong itakda sa usa sa tulo ka mga kantidad:

* wala: Walay aksyon nga gihimo, ang resulta lang sa verification ang ibalik sa nagpadala pinaagi sa email reporting mechanism.
* Quarantine: Ibutang ang mail nga wala nakapasar sa verification sa spam folder, apan dili direkta nga isalikway ang mail.
* Isalikway: Direkta nga isalikway ang mga email nga napakyas sa pag-verify.

### fo (Mga Opsyon sa Pagkapakyas)

Gipiho ang gidaghanon sa impormasyon nga gibalik sa mekanismo sa pagtaho. Mahimo kining itakda sa usa sa mosunod nga mga bili:

* 0: I-report ang mga resulta sa validation para sa tanang mensahe
* 1: I-report lang ang mga mensahe nga napakyas sa pag-verify
* d: I-report lang ang mga kapakyasan sa pag-verify sa ngalan sa domain
* s: i-report lang ang mga kapakyasan sa pag-verify sa SPF
* l: I-report lang ang mga kapakyasan sa pag-verify sa DKIM

### rua ug ruf

* rua (Pagreport sa URI alang sa Aggregate nga mga taho): Email address alang sa pagdawat sa giipon nga mga taho
* ruf (Pagreport sa URI alang sa Forensic nga mga taho): adres sa email aron makadawat mga detalyado nga taho

## Idugang ang mga rekord sa MX aron ipasa ang mga email sa Google Mail

Tungod kay wala koy nakit-an nga libre nga mailbox sa korporasyon nga nagsuporta sa mga unibersal nga adres (Catch-All, makadawat sa bisan unsang mga email nga ipadala sa kini nga domain name, nga wala’y pagdili sa mga prefix), gigamit nako ang chasquid aron ipasa ang tanan nga mga email sa akong mailbox sa Gmail.

**Kung ikaw adunay imong kaugalingon nga bayad nga mailbox sa negosyo, palihug ayaw usba ang MX ug laktawan kini nga lakang.**

I-edit `conf/chasquid/domains/wac.tax/aliases` , itakda ang forwarding mailbox

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/OBDl2gw.webp)

`*` nagpaila sa tanan nga mga email, `i` ang prefix sa email address sa nagpadala nga tiggamit nga gihimo sa ibabaw. Aron ipadala ang mail, ang matag user kinahanglan nga magdugang usa ka linya.

Dayon idugang ang MX record (direkta kong itudlo ang adres sa reverse domain name dinhi, sama sa gipakita sa unang linya sa hulagway sa ubos).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/7__KrU8.webp)

Human makompleto ang configuration, mahimo nimong gamiton ang ubang mga email address aron magpadala og mga email ngadto sa `i@wac.tax` ug `any123@wac.tax` aron makita kung makadawat ka ba og mga email sa Gmail.

Kung dili, susiha ang chasquid log ( `grep chasquid /var/log/syslog` ).

## Pagpadala ug email sa i@wac.tax gamit ang Google Mail

Human nadawat sa Google Mail ang mail, natural nga naglaum ko nga motubag gamit `i@wac.tax` imbes nga i.wac.tax@gmail.com.

Bisitaha ang [https://mail.google.com/mail/u/1/#settings/accounts](https://mail.google.com/mail/u/1/#settings/accounts) ug i-klik ang "Add another email address".

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/PAvyE3C.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/_OgLsPT.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/XIUf6Dc.webp)

Dayon, isulod ang verification code nga nadawat sa email nga gipasa.

Sa katapusan, mahimo kining itakda isip default nga adres sa nagpadala (uban ang kapilian sa pagtubag sa parehas nga adres).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/a95dO60.webp)

Niining paagiha, nahuman na namo ang pagtukod sa SMTP mail server ug sa samang higayon naggamit sa Google Mail sa pagpadala ug pagdawat sa mga email.

## Pagpadala usa ka pagsulay nga email aron masusi kung malampuson ba ang pag-configure

Pagsulod `ops/chasquid`

Ang run `direnv allow` sa pag-instalar sa mga dependency (ang direnv na-install sa miaging usa ka yawe nga proseso sa pagsugod ug usa ka kaw-it ang gidugang sa kabhang)

unya dagan

```
user=i@wac.tax pass=xxxx to=iuser.link@gmail.com ./sendmail.coffee
```

Ang kahulogan sa mga parametro mao ang mosunod

* user: SMTP username
* pass: SMTP password
* sa: tigdawat

Mahimo ka magpadala usa ka pagsulay nga email.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ae1iWyM.webp)

Girekomenda nga gamiton ang Gmail aron makadawat mga pagsulay nga email aron masusi kung malampuson ba ang mga pag-configure.

### TLS standard encryption

Sama sa gipakita sa numero sa ubos, adunay kini nga gamay nga kandado, nga nagpasabut nga ang sertipiko sa SSL malampuson nga nahimo.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/SrdbAwh.webp)

Dayon i-klik ang "Ipakita ang Orihinal nga Email"

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/qQQsdxg.webp)

### DKIM

Sama sa gipakita sa hulagway sa ubos, ang Gmail orihinal nga mail page nagpakita sa DKIM, nga nagpasabot nga ang DKIM configuration malampuson.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/an6aXK6.webp)

Susiha ang Nadawat sa ulohan sa orihinal nga email, ug imong makita nga ang nagpadala nga adres kay IPV6, nga nagpasabot nga ang IPV6 malampuson usab nga na-configure.
