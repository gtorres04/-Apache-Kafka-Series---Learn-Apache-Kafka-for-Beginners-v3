# 57. Wikimedia Producer Project Setup

Wikimedia Recent Change Stream: https://stream.wikimedia.org/v2/stream/recentchange
Demo 1: https://codepen.io/Krinkle/pen/BwEKgW?editors=1010
Demo 2: https://esjewett.github.io/wm-eventsource-demo/

Se crea el proyecto gradle con las dependencias necesarias para iniciar con la implementacion

# 58. Wikimedia Producer Implementation

Se implementa el controlador de los eventos recibidos HTTP y el productor que tomara ese mensaje y lo enviara a Kafka

# 59. Wikimedia Producer Run

* Se crea el topico wikimedia.recentchange
```
kafka-topics --bootstrap-server localhost:9092 --create --topic wikimedia.recentchange --partitions 3 --replication-factor 1
```

* Se ejecuta el productor

```
> Task :kafka-producer-wikimedia:WikimediaChangesProducer.main()
[main] INFO org.apache.kafka.clients.producer.ProducerConfig - ProducerConfig values: 
	acks = -1
	batch.size = 16384
	bootstrap.servers = [127.0.0.1:9092]
	buffer.memory = 33554432
	client.dns.lookup = use_all_dns_ips
	client.id = producer-1
	compression.type = none
	connections.max.idle.ms = 540000
	delivery.timeout.ms = 120000
	enable.idempotence = true
	interceptor.classes = []
	key.serializer = class org.apache.kafka.common.serialization.StringSerializer
	linger.ms = 0
	max.block.ms = 60000
	max.in.flight.requests.per.connection = 5
	max.request.size = 1048576
	metadata.max.age.ms = 300000
	metadata.max.idle.ms = 300000
	metric.reporters = []
	metrics.num.samples = 2
	metrics.recording.level = INFO
	metrics.sample.window.ms = 30000
	partitioner.class = class org.apache.kafka.clients.producer.internals.DefaultPartitioner
	receive.buffer.bytes = 32768
	reconnect.backoff.max.ms = 1000
	reconnect.backoff.ms = 50
	request.timeout.ms = 30000
	retries = 2147483647
	retry.backoff.ms = 100
	sasl.client.callback.handler.class = null
	sasl.jaas.config = null
	sasl.kerberos.kinit.cmd = /usr/bin/kinit
	sasl.kerberos.min.time.before.relogin = 60000
	sasl.kerberos.service.name = null
	sasl.kerberos.ticket.renew.jitter = 0.05
	sasl.kerberos.ticket.renew.window.factor = 0.8
	sasl.login.callback.handler.class = null
	sasl.login.class = null
	sasl.login.connect.timeout.ms = null
	sasl.login.read.timeout.ms = null
	sasl.login.refresh.buffer.seconds = 300
	sasl.login.refresh.min.period.seconds = 60
	sasl.login.refresh.window.factor = 0.8
	sasl.login.refresh.window.jitter = 0.05
	sasl.login.retry.backoff.max.ms = 10000
	sasl.login.retry.backoff.ms = 100
	sasl.mechanism = GSSAPI
	sasl.oauthbearer.clock.skew.seconds = 30
	sasl.oauthbearer.expected.audience = null
	sasl.oauthbearer.expected.issuer = null
	sasl.oauthbearer.jwks.endpoint.refresh.ms = 3600000
	sasl.oauthbearer.jwks.endpoint.retry.backoff.max.ms = 10000
	sasl.oauthbearer.jwks.endpoint.retry.backoff.ms = 100
	sasl.oauthbearer.jwks.endpoint.url = null
	sasl.oauthbearer.scope.claim.name = scope
	sasl.oauthbearer.sub.claim.name = sub
	sasl.oauthbearer.token.endpoint.url = null
	security.protocol = PLAINTEXT
	security.providers = null
	send.buffer.bytes = 131072
	socket.connection.setup.timeout.max.ms = 30000
	socket.connection.setup.timeout.ms = 10000
	ssl.cipher.suites = null
	ssl.enabled.protocols = [TLSv1.2, TLSv1.3]
	ssl.endpoint.identification.algorithm = https
	ssl.engine.factory.class = null
	ssl.key.password = null
	ssl.keymanager.algorithm = SunX509
	ssl.keystore.certificate.chain = null
	ssl.keystore.key = null
	ssl.keystore.location = null
	ssl.keystore.password = null
	ssl.keystore.type = JKS
	ssl.protocol = TLSv1.3
	ssl.provider = null
	ssl.secure.random.implementation = null
	ssl.trustmanager.algorithm = PKIX
	ssl.truststore.certificates = null
	ssl.truststore.location = null
	ssl.truststore.password = null
	ssl.truststore.type = JKS
	transaction.timeout.ms = 60000
	transactional.id = null
	value.serializer = class org.apache.kafka.common.serialization.StringSerializer

[main] INFO org.apache.kafka.common.utils.AppInfoParser - Kafka version: 3.1.0
[main] INFO org.apache.kafka.common.utils.AppInfoParser - Kafka commitId: 37edeed0777bacb3
[main] INFO org.apache.kafka.common.utils.AppInfoParser - Kafka startTimeMs: 1656023805385
[main] INFO com.launchdarkly.eventsource.EventSource - Starting EventSource client using URI: https://stream.wikimedia.org/v2/stream/recentchange
[kafka-producer-network-thread | producer-1] INFO org.apache.kafka.clients.Metadata - [Producer clientId=producer-1] Cluster ID: Uu9pnoNSRxashKBRdPCtfQ
[okhttp-eventsource-stream-[]-0] INFO com.launchdarkly.eventsource.EventSource - Connected to EventSource stream.
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://en.wikipedia.org/wiki/Category:Pages_using_duplicate_arguments_in_template_calls","request_id":"7ca03fc7-cd84-4d33-98e4-7d40e7b8b425","id":"3624dcc3-d01b-488e-9a70-9737abbd1cce","dt":"2022-06-23T22:36:41Z","domain":"en.wikipedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949425},"id":1519050472,"type":"categorize","namespace":14,"title":"Category:Pages using duplicate arguments in template calls","comment":"[[:Springwood School]] added to category, [[Special:WhatLinksHere/Springwood School|this page is included within other pages]]","timestamp":1656023801,"user":"2600:1700:1D90:5CF0:90BB:77CA:AFBD:C0A9","bot":false,"server_url":"https://en.wikipedia.org","server_name":"en.wikipedia.org","server_script_path":"/w","wiki":"enwiki","parsedcomment":"<a href=\"/wiki/Springwood_School\" title=\"Springwood School\">Springwood School</a> added to category, <a href=\"/wiki/Special:WhatLinksHere/Springwood_School\" title=\"Special:WhatLinksHere/Springwood School\">this page is included within other pages</a>"}
[kafka-producer-network-thread | producer-1] INFO org.apache.kafka.clients.Metadata - [Producer clientId=producer-1] Resetting the last seen epoch of partition wikimedia.recentchange-0 to 0 since the associated topicId changed from null to nwDtYeBbRuWc_zNJQy1glw
[kafka-producer-network-thread | producer-1] INFO org.apache.kafka.clients.Metadata - [Producer clientId=producer-1] Resetting the last seen epoch of partition wikimedia.recentchange-1 to 0 since the associated topicId changed from null to nwDtYeBbRuWc_zNJQy1glw
[kafka-producer-network-thread | producer-1] INFO org.apache.kafka.clients.Metadata - [Producer clientId=producer-1] Resetting the last seen epoch of partition wikimedia.recentchange-2 to 0 since the associated topicId changed from null to nwDtYeBbRuWc_zNJQy1glw
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://cy.wikipedia.org/wiki/Categori:Trefi_Apache_County","request_id":"aadb8570-ceb6-4741-a7f0-4c46ac21578a","id":"295ceca8-7870-4063-bec2-d993879fd774","dt":"2022-06-23T22:36:41Z","domain":"cy.wikipedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949426},"id":21362523,"type":"categorize","namespace":14,"title":"Categori:Trefi Apache County","comment":"Symudwyd [[:Eagar, Arizona]] o'r categori","timestamp":1656023801,"user":"Craigysgafn","bot":false,"server_url":"https://cy.wikipedia.org","server_name":"cy.wikipedia.org","server_script_path":"/w","wiki":"cywiki","parsedcomment":"Symudwyd <a href=\"/wiki/Eagar,_Arizona\" title=\"Eagar, Arizona\">Eagar, Arizona</a> o&#039;r categori"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://commons.wikimedia.org/wiki/File:Historja_naturalna_-_zoologja,_botanika,_mineralogja,_gieologja_1900_(118597992).jpg","request_id":"90acbd7e-d343-4388-84b6-6d0e0ab8057a","id":"a096969d-ab6f-469e-9614-7d57cd70fe22","dt":"2022-06-23T22:36:45Z","domain":"commons.wikimedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949427},"id":1954751689,"type":"edit","namespace":6,"title":"File:Historja naturalna - zoologja, botanika, mineralogja, gieologja 1900 (118597992).jpg","comment":"/* wbeditentity-update:0| */ automatically modify [[Commons:Structured data|structured data]] based on file information: meta","timestamp":1656023805,"user":"SchlurcherBot","bot":true,"minor":false,"patrolled":true,"length":{"old":1057,"new":1539},"revision":{"old":634194703,"new":667834889},"server_url":"https://commons.wikimedia.org","server_name":"commons.wikimedia.org","server_script_path":"/w","wiki":"commonswiki","parsedcomment":"‎<span dir=\"auto\"><span class=\"autocomment\">Ein Objekt geändert: </span></span> automatically modify <a href=\"/wiki/Commons:Structured_data\" title=\"Commons:Structured data\">structured data</a> based on file information: meta"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://ar.wikipedia.org/wiki/%D8%AA%D9%8A%D8%A8%D9%88%D8%A7%D9%86%D8%A7","request_id":"59149261-29d1-468d-b0a1-e5abd10b88c3","id":"f16d1262-d3b8-4862-b1e7-c9a0d0136c12","dt":"2022-06-23T22:36:41Z","domain":"ar.wikipedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949428},"id":271731342,"type":"edit","namespace":0,"title":"تيبوانا","comment":"بوت:إضافة ضبط استنادي (1)","timestamp":1656023801,"user":"JarBot","bot":true,"minor":true,"length":{"old":3618,"new":3642},"revision":{"old":48254240,"new":58480098},"server_url":"https://ar.wikipedia.org","server_name":"ar.wikipedia.org","server_script_path":"/w","wiki":"arwiki","parsedcomment":"بوت:إضافة ضبط استنادي (1)"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://ru.wikisource.org/wiki/%D0%9A%D0%B0%D1%82%D0%B5%D0%B3%D0%BE%D1%80%D0%B8%D1%8F:%D0%98%D0%BC%D0%BF%D0%BE%D1%80%D1%82/az.lib.ru/%D0%92%D0%BB%D0%B0%D0%B4%D0%B8%D0%BC%D0%B8%D1%80_%D0%9C%D0%B0%D0%BA%D1%81%D0%B8%D0%BC%D0%BE%D0%B2%D0%B8%D1%87_%D0%A4%D1%80%D0%B8%D1%87%D0%B5","request_id":"fb7351f9-c95b-4270-931f-97dd67972731","id":"e5387cf0-ca09-4a2a-b82a-6fe9721ef4a5","dt":"2022-06-23T22:36:44Z","domain":"ru.wikisource.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949429},"id":15177405,"type":"categorize","namespace":14,"title":"Категория:Импорт/az.lib.ru/Владимир Максимович Фриче","comment":"[[:Поэзия кошмаров и ужаса (Фриче)]] добавлена в категорию","timestamp":1656023804,"user":"TextworkerBot","bot":true,"server_url":"https://ru.wikisource.org","server_name":"ru.wikisource.org","server_script_path":"/w","wiki":"ruwikisource","parsedcomment":"<a href=\"/wiki/%D0%9F%D0%BE%D1%8D%D0%B7%D0%B8%D1%8F_%D0%BA%D0%BE%D1%88%D0%BC%D0%B0%D1%80%D0%BE%D0%B2_%D0%B8_%D1%83%D0%B6%D0%B0%D1%81%D0%B0_(%D0%A4%D1%80%D0%B8%D1%87%D0%B5)\" title=\"Поэзия кошмаров и ужаса (Фриче)\">Поэзия кошмаров и ужаса (Фриче)</a> добавлена в категорию"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://commons.wikimedia.org/wiki/Category:Corpus_Christi_solemnity_2005_in_Toledo,_Spain","request_id":"81ed14ed-d9d5-4e0b-adf0-123cdbe7b83d","id":"b89019eb-f17e-477b-8194-eedf4af4f153","dt":"2022-06-23T22:36:46Z","domain":"commons.wikimedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949430},"id":1954751690,"type":"edit","namespace":14,"title":"Category:Corpus Christi solemnity 2005 in Toledo, Spain","comment":"added [[Category:2005 in Toledo, Spain]]","timestamp":1656023806,"user":"JMCC1","bot":false,"minor":true,"patrolled":true,"length":{"old":93,"new":128},"revision":{"old":240811847,"new":667834890},"server_url":"https://commons.wikimedia.org","server_name":"commons.wikimedia.org","server_script_path":"/w","wiki":"commonswiki","parsedcomment":"added <a href=\"/w/index.php?title=Category:2005_in_Toledo,_Spain&amp;action=edit&amp;redlink=1\" class=\"new\" title=\"Category:2005 in Toledo, Spain (page does not exist)\">Category:2005 in Toledo, Spain</a>"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://www.wikidata.org/wiki/Q56743108","request_id":"eb4c54ea-dc8a-4ef5-94cc-ba59141364b7","id":"3b7599ee-73dc-457f-8224-bd6ec2e9aadf","dt":"2022-06-23T22:36:45Z","domain":"www.wikidata.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949431},"id":1717965667,"type":"edit","namespace":0,"title":"Q56743108","comment":"/* wbeditentity-update:0| */ Author Disambiguator set author [[Q112671676]] ([[:toollabs:editgroups/b/AD/4725a733|details]])","timestamp":1656023805,"user":"Trilotat","bot":false,"minor":false,"patrolled":true,"length":{"old":1554807,"new":1555124},"revision":{"old":1665766723,"new":1665827203},"server_url":"https://www.wikidata.org","server_name":"www.wikidata.org","server_script_path":"/w","wiki":"wikidatawiki","parsedcomment":"‎<span dir=\"auto\"><span class=\"autocomment\">Updated Item: </span></span> Author Disambiguator set author <a href=\"/wiki/Q112671676\" title=\"Q112671676\">Q112671676</a> (<a href=\"https://iw.toolforge.org/editgroups/b/AD/4725a733\" class=\"extiw\" title=\"toollabs:editgroups/b/AD/4725a733\">details</a>)"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://en.wikipedia.org/wiki/List_of_shipwrecks_in_1995","request_id":"7d3cbe3f-b00f-48c6-9aeb-32bbfe0e9d0f","id":"c9db19ba-76d4-4f4e-8bf9-33fe4c90b29f","dt":"2022-06-23T22:36:44Z","domain":"en.wikipedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949432},"id":1519050474,"type":"edit","namespace":0,"title":"List of shipwrecks in 1995","comment":"[[WP:AWB]] [[WP:CHECKWIKI]]  16/90/91 cleanup, et. al.","timestamp":1656023804,"user":"Ost316","bot":false,"minor":false,"length":{"old":29413,"new":29550},"revision":{"old":1076742135,"new":1094673803},"server_url":"https://en.wikipedia.org","server_name":"en.wikipedia.org","server_script_path":"/w","wiki":"enwiki","parsedcomment":"<a href=\"/wiki/Wikipedia:AWB\" class=\"mw-redirect\" title=\"Wikipedia:AWB\">WP:AWB</a> <a href=\"/wiki/Wikipedia:CHECKWIKI\" class=\"mw-redirect\" title=\"Wikipedia:CHECKWIKI\">WP:CHECKWIKI</a>  16/90/91 cleanup, et. al."}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://pt.wikipedia.org/wiki/Usu%C3%A1rio(a):Jo%C3%A3o_El_Rat%C3%B3n","request_id":"9ebe16e6-47f4-4e41-86f2-746c26557b9c","id":"31ebbd26-7269-4d22-973f-7c695319c24a","dt":"2022-06-23T22:36:46Z","domain":"pt.wikipedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949433},"id":114387582,"type":"log","namespace":2,"title":"Usuário(a):João El Ratón","comment":"","timestamp":1656023806,"user":"João El Ratón","bot":false,"log_id":29663103,"log_type":"newusers","log_action":"create","log_params":{"userid":2777989},"log_action_comment":"New user account","server_url":"https://pt.wikipedia.org","server_name":"pt.wikipedia.org","server_script_path":"/w","wiki":"ptwiki","parsedcomment":""}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://en.wikipedia.org/wiki/Wikipedia_talk:Short_description","request_id":"62355cb0-2814-4a4f-bd6f-b1f161f4da74","id":"facb599a-6786-48cf-9611-e89f5fa4e99b","dt":"2022-06-23T22:36:45Z","domain":"en.wikipedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949434},"id":1519050473,"type":"edit","namespace":5,"title":"Wikipedia talk:Short description","comment":"/* Reasons why SDs over 40 characters are a problem */ Reply","timestamp":1656023805,"user":"Kvng","bot":false,"minor":false,"length":{"old":54957,"new":55332},"revision":{"old":1094672990,"new":1094673802},"server_url":"https://en.wikipedia.org","server_name":"en.wikipedia.org","server_script_path":"/w","wiki":"enwiki","parsedcomment":"<span dir=\"auto\"><span class=\"autocomment\"><a href=\"/wiki/Wikipedia_talk:Short_description#Reasons_why_SDs_over_40_characters_are_a_problem\" title=\"Wikipedia talk:Short description\">→‎Reasons why SDs over 40 characters are a problem</a>: </span> Reply</span>"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://commons.wikimedia.org/wiki/Category:2005_in_Toledo,_Spain","request_id":"81ed14ed-d9d5-4e0b-adf0-123cdbe7b83d","id":"ecd05cc8-5002-4273-add8-42dcbf223226","dt":"2022-06-23T22:36:46Z","domain":"commons.wikimedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949435},"id":1954751691,"type":"categorize","namespace":14,"title":"Category:2005 in Toledo, Spain","comment":"[[:Category:Corpus Christi solemnity 2005 in Toledo, Spain]] added to category","timestamp":1656023806,"user":"JMCC1","bot":false,"server_url":"https://commons.wikimedia.org","server_name":"commons.wikimedia.org","server_script_path":"/w","wiki":"commonswiki","parsedcomment":"<a href=\"/wiki/Category:Corpus_Christi_solemnity_2005_in_Toledo,_Spain\" title=\"Category:Corpus Christi solemnity 2005 in Toledo, Spain\">Category:Corpus Christi solemnity 2005 in Toledo, Spain</a> added to category"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://www.wikidata.org/wiki/Q1592840","request_id":"1a1b982a-7c3f-4d21-887d-911d35f2d3f8","id":"8de791e2-36bb-45c2-9bd9-4a2d43f1dbc2","dt":"2022-06-23T22:36:46Z","domain":"www.wikidata.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949436},"id":1717965669,"type":"edit","namespace":0,"title":"Q1592840","comment":"/* wbeditentity-update:0| */ +trwiki","timestamp":1656023806,"user":"Louperibot","bot":true,"minor":false,"patrolled":true,"length":{"old":17131,"new":17219},"revision":{"old":1544450716,"new":1665827206},"server_url":"https://www.wikidata.org","server_name":"www.wikidata.org","server_script_path":"/w","wiki":"wikidatawiki","parsedcomment":"‎<span dir=\"auto\"><span class=\"autocomment\">Élément modifié : </span></span> +trwiki"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://www.wikidata.org/wiki/Q60180482","request_id":"0d861a01-6df7-4073-92ea-144fe84c69f2","id":"b8d05c79-e509-4f88-ab10-71c5bd57530a","dt":"2022-06-23T22:36:46Z","domain":"www.wikidata.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949437},"id":1717965668,"type":"edit","namespace":0,"title":"Q60180482","comment":"/* wbsetclaim-create:2||1 */ [[Property:P156]]: [[Q79626486]]","timestamp":1656023806,"user":"J 1982","bot":false,"minor":false,"patrolled":true,"length":{"old":17095,"new":17529},"revision":{"old":1665827195,"new":1665827205},"server_url":"https://www.wikidata.org","server_name":"www.wikidata.org","server_script_path":"/w","wiki":"wikidatawiki","parsedcomment":"‎<span dir=\"auto\"><span class=\"autocomment\">Created claim: </span></span> <a href=\"/wiki/Property:P156\" title=\"Property:P156\">Property:P156</a>: <a href=\"/wiki/Q79626486\" title=\"Q79626486\">Q79626486</a>"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://en.wikipedia.org/wiki/Glasgow_Clan","request_id":"46cefad3-bf73-4a5e-8364-690e1853b48a","id":"192525c2-7b8c-40ae-b8ee-f61b3af7f2bb","dt":"2022-06-23T22:36:45Z","domain":"en.wikipedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949438},"id":1519050475,"type":"edit","namespace":0,"title":"Glasgow Clan","comment":"Clan","timestamp":1656023805,"user":"67.241.60.112","bot":false,"minor":false,"length":{"old":52025,"new":52029},"revision":{"old":1094458838,"new":1094673801},"server_url":"https://en.wikipedia.org","server_name":"en.wikipedia.org","server_script_path":"/w","wiki":"enwiki","parsedcomment":"Clan"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://fr.wikisource.org/wiki/Page:Earl_Derr_Biggers_-_Le_Perroquet_chinois,_paru_dans_Ric_et_Rac,_1931-1932.djvu/147","request_id":"f397418c-a7f1-475a-a474-8a2d33cb4654","id":"e52ba8aa-67b5-4fda-abbb-0dbb37d6dd1a","dt":"2022-06-23T22:36:46Z","domain":"fr.wikisource.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949439},"id":18596483,"type":"edit","namespace":104,"title":"Page:Earl Derr Biggers - Le Perroquet chinois, paru dans Ric et Rac, 1931-1932.djvu/147","comment":"/* Corrigée */","timestamp":1656023806,"user":"Lorlam","bot":false,"minor":false,"patrolled":true,"length":{"old":1907,"new":2217},"revision":{"old":12533010,"new":12535619},"server_url":"https://fr.wikisource.org","server_name":"fr.wikisource.org","server_script_path":"/w","wiki":"frwikisource","parsedcomment":"<span dir=\"auto\"><span class=\"autocomment\"><a href=\"/wiki/Page:Earl_Derr_Biggers_-_Le_Perroquet_chinois,_paru_dans_Ric_et_Rac,_1931-1932.djvu/147#Corrigée\" title=\"Page:Earl Derr Biggers - Le Perroquet chinois, paru dans Ric et Rac, 1931-1932.djvu/147\">→‎Corrigée</a></span></span>"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://es.wikipedia.org/wiki/Administraci%C3%B3n_de_Carlos_Alvarado","request_id":"66158230-40b1-4a56-82e7-cc3a414e2b34","id":"a89808ac-3bf0-4e79-b1de-47b77d7beefb","dt":"2022-06-23T22:36:43Z","domain":"es.wikipedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949440},"id":268972492,"type":"edit","namespace":0,"title":"Administración de Carlos Alvarado","comment":"Actualización","timestamp":1656023803,"user":"Js1597","bot":false,"minor":false,"length":{"old":8310,"new":62817},"revision":{"old":143650892,"new":144370801},"server_url":"https://es.wikipedia.org","server_name":"es.wikipedia.org","server_script_path":"/w","wiki":"eswiki","parsedcomment":"Actualización"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://en.wiktionary.org/wiki/%D1%8B%D0%B0%D0%BB%D0%B4%D1%8C%D1%8B%D1%82","request_id":"a339edee-6e2d-4f56-97b5-eee9b257110d","id":"0f8a7b59-a91a-4f84-8ab4-a39ae4f513d3","dt":"2022-06-23T22:36:47Z","domain":"en.wiktionary.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949441},"id":94814034,"type":"edit","namespace":0,"title":"ыалдьыт","comment":"","timestamp":1656023807,"user":"216.234.200.179","bot":false,"minor":false,"patrolled":false,"length":{"old":137,"new":153},"revision":{"old":67320581,"new":67528777},"server_url":"https://en.wiktionary.org","server_name":"en.wiktionary.org","server_script_path":"/w","wiki":"enwiktionary","parsedcomment":""}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://commons.wikimedia.org/wiki/Category:Barc%C3%A9s_River","request_id":"e26104e0-f6fe-4383-9a09-449b90ee5172","id":"2e18a842-d53a-43c1-9a8e-647f7e2233c2","dt":"2022-06-23T22:36:43Z","domain":"commons.wikimedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949442},"id":1954751692,"type":"edit","namespace":14,"title":"Category:Barcés River","comment":"removed [[Category:Mero River]]; added [[Category:Tributaries of the Mero River]]","timestamp":1656023803,"user":"Tyk","bot":false,"minor":true,"patrolled":true,"length":{"old":137,"new":163},"revision":{"old":667833114,"new":667834891},"server_url":"https://commons.wikimedia.org","server_name":"commons.wikimedia.org","server_script_path":"/w","wiki":"commonswiki","parsedcomment":"removed <a href=\"/wiki/Category:Mero_River\" title=\"Category:Mero River\">Category:Mero River</a>; added <a href=\"/wiki/Category:Tributaries_of_the_Mero_River\" title=\"Category:Tributaries of the Mero River\">Category:Tributaries of the Mero River</a>"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://commons.wikimedia.org/wiki/File:ISS064-E-55765_-_View_of_Earth.jpg","request_id":"085689a2-c45f-43af-a17c-b8f477361925","id":"490158d7-f20d-4f61-86a0-5d212dc1951f","dt":"2022-06-23T22:36:46Z","domain":"commons.wikimedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949443},"id":1954751693,"type":"log","namespace":6,"title":"File:ISS064-E-55765 - View of Earth.jpg","comment":"","timestamp":1656023806,"user":"AskeBot","bot":true,"log_id":326258373,"log_type":"upload","log_action":"upload","log_params":{"img_sha1":"1jrczpq9wujms6fj5i6jxkyk0vmmyrr","img_timestamp":"20220623223646"},"log_action_comment":"uploaded &quot;[[File:ISS064-E-55765 - View of Earth.jpg]]&quot;","server_url":"https://commons.wikimedia.org","server_name":"commons.wikimedia.org","server_script_path":"/w","wiki":"commonswiki","parsedcomment":""}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://it.wikipedia.org/wiki/Antonio_d%27Orl%C3%A9ans_(1824-1890)","request_id":"ab32af9c-4630-40d8-bbbe-f8fa7cd28dbc","id":"3dcd1e23-edc8-481d-a990-b6626bab3e8f","dt":"2022-06-23T22:36:46Z","domain":"it.wikipedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949444},"id":307285359,"type":"edit","namespace":0,"title":"Antonio d'Orléans (1824-1890)","comment":"","timestamp":1656023806,"user":"Basilicofresco","bot":false,"minor":false,"patrolled":true,"length":{"old":12316,"new":12317},"revision":{"old":128039016,"new":128039417},"server_url":"https://it.wikipedia.org","server_name":"it.wikipedia.org","server_script_path":"/w","wiki":"itwiki","parsedcomment":""}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://commons.wikimedia.org/wiki/File:Bruxelles-tapis-fleurs-2010-3.JPG","request_id":"b7352e5d-23bc-408c-8f6c-46a8d7133a7d","id":"7a998094-27e4-4b3c-a494-fb472b23df43","dt":"2022-06-23T22:36:46Z","domain":"commons.wikimedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949445},"id":1954751695,"type":"edit","namespace":6,"title":"File:Bruxelles-tapis-fleurs-2010-3.JPG","comment":"/* wbcreateclaim-create:1| */ [[d:Special:EntityPage/P180]]: [[d:Special:EntityPage/Q25112794]], Setting a depicts statement using Depictor","timestamp":1656023806,"user":"Thibaultmol","bot":false,"minor":false,"patrolled":false,"length":{"old":5761,"new":6195},"revision":{"old":469809228,"new":667834893},"server_url":"https://commons.wikimedia.org","server_name":"commons.wikimedia.org","server_script_path":"/w","wiki":"commonswiki","parsedcomment":"‎<span dir=\"auto\"><span class=\"autocomment\">Created claim: </span></span> <a href=\"https://www.wikidata.org/wiki/Special:EntityPage/P180\" class=\"extiw\" title=\"d:Special:EntityPage/P180\">d:Special:EntityPage/P180</a>: <a href=\"https://www.wikidata.org/wiki/Special:EntityPage/Q25112794\" class=\"extiw\" title=\"d:Special:EntityPage/Q25112794\">d:Special:EntityPage/Q25112794</a>, Setting a depicts statement using Depictor"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://commons.wikimedia.org/wiki/Category:Images_from_the_Earth_Science_and_Remote_Sensing_Unit,_Lyndon_B._Johnson_Space_Center","request_id":"e38b0397-7b9d-4ee0-b05f-0b027e2a155d","id":"f3573868-dfe7-4b0b-96dc-a0442bd352f6","dt":"2022-06-23T22:36:44Z","domain":"commons.wikimedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949446},"id":1954751694,"type":"categorize","namespace":14,"title":"Category:Images from the Earth Science and Remote Sensing Unit, Lyndon B. Johnson Space Center","comment":"[[:File:ISS043-E-75414 - View of Earth.jpg]] added to category","timestamp":1656023804,"user":"AskeBot","bot":true,"server_url":"https://commons.wikimedia.org","server_name":"commons.wikimedia.org","server_script_path":"/w","wiki":"commonswiki","parsedcomment":"<a href=\"/wiki/File:ISS043-E-75414_-_View_of_Earth.jpg\" title=\"File:ISS043-E-75414 - View of Earth.jpg\">File:ISS043-E-75414 - View of Earth.jpg</a> added to category"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://commons.wikimedia.org/wiki/Category:Pages_with_camera_coordinates_from_SDC","request_id":"e38b0397-7b9d-4ee0-b05f-0b027e2a155d","id":"f17e73f2-08d1-48d0-9a6c-65f2d92a1d25","dt":"2022-06-23T22:36:44Z","domain":"commons.wikimedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949447},"id":1954751696,"type":"categorize","namespace":14,"title":"Category:Pages with camera coordinates from SDC","comment":"[[:File:ISS043-E-75414 - View of Earth.jpg]] added to category","timestamp":1656023804,"user":"AskeBot","bot":true,"server_url":"https://commons.wikimedia.org","server_name":"commons.wikimedia.org","server_script_path":"/w","wiki":"commonswiki","parsedcomment":"<a href=\"/wiki/File:ISS043-E-75414_-_View_of_Earth.jpg\" title=\"File:ISS043-E-75414 - View of Earth.jpg\">File:ISS043-E-75414 - View of Earth.jpg</a> added to category"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://fr.wikisource.org/wiki/Cat%C3%A9gorie:Page_corrig%C3%A9e","request_id":"f397418c-a7f1-475a-a474-8a2d33cb4654","id":"7af66f31-28e5-42f1-8378-d9f6ad1d0361","dt":"2022-06-23T22:36:46Z","domain":"fr.wikisource.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949448},"id":18596484,"type":"categorize","namespace":14,"title":"Catégorie:Page corrigée","comment":"[[:Page:Earl Derr Biggers - Le Perroquet chinois, paru dans Ric et Rac, 1931-1932.djvu/147]] ajoutée à la catégorie, [[Special:WhatLinksHere/Page:Earl Derr Biggers - Le Perroquet chinois, paru dans Ric et Rac, 1931-1932.djvu/147|cette page est liée ou incluse dans d’autres pages]]","timestamp":1656023806,"user":"Lorlam","bot":false,"server_url":"https://fr.wikisource.org","server_name":"fr.wikisource.org","server_script_path":"/w","wiki":"frwikisource","parsedcomment":"<a href=\"/wiki/Page:Earl_Derr_Biggers_-_Le_Perroquet_chinois,_paru_dans_Ric_et_Rac,_1931-1932.djvu/147\" title=\"Page:Earl Derr Biggers - Le Perroquet chinois, paru dans Ric et Rac, 1931-1932.djvu/147\">Page:Earl Derr Biggers - Le Perroquet chinois, paru dans Ric et Rac, 1931-1932.djvu/147</a> ajoutée à la catégorie, <a href=\"/wiki/Sp%C3%A9cial:Pages_li%C3%A9es/Page:Earl_Derr_Biggers_-_Le_Perroquet_chinois,_paru_dans_Ric_et_Rac,_1931-1932.djvu/147\" title=\"Spécial:Pages liées/Page:Earl Derr Biggers - Le Perroquet chinois, paru dans Ric et Rac, 1931-1932.djvu/147\">cette page est liée ou incluse dans d’autres pages</a>"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://fr.wikisource.org/wiki/Cat%C3%A9gorie:Page_non_corrig%C3%A9e","request_id":"f397418c-a7f1-475a-a474-8a2d33cb4654","id":"32c04778-c942-4609-9941-faed1c8d8463","dt":"2022-06-23T22:36:46Z","domain":"fr.wikisource.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949449},"id":18596485,"type":"categorize","namespace":14,"title":"Catégorie:Page non corrigée","comment":"[[:Page:Earl Derr Biggers - Le Perroquet chinois, paru dans Ric et Rac, 1931-1932.djvu/147]] supprimée de la catégorie, [[Special:WhatLinksHere/Page:Earl Derr Biggers - Le Perroquet chinois, paru dans Ric et Rac, 1931-1932.djvu/147|cette page est liée ou incluse dans d’autres pages]]","timestamp":1656023806,"user":"Lorlam","bot":false,"server_url":"https://fr.wikisource.org","server_name":"fr.wikisource.org","server_script_path":"/w","wiki":"frwikisource","parsedcomment":"<a href=\"/wiki/Page:Earl_Derr_Biggers_-_Le_Perroquet_chinois,_paru_dans_Ric_et_Rac,_1931-1932.djvu/147\" title=\"Page:Earl Derr Biggers - Le Perroquet chinois, paru dans Ric et Rac, 1931-1932.djvu/147\">Page:Earl Derr Biggers - Le Perroquet chinois, paru dans Ric et Rac, 1931-1932.djvu/147</a> supprimée de la catégorie, <a href=\"/wiki/Sp%C3%A9cial:Pages_li%C3%A9es/Page:Earl_Derr_Biggers_-_Le_Perroquet_chinois,_paru_dans_Ric_et_Rac,_1931-1932.djvu/147\" title=\"Spécial:Pages liées/Page:Earl Derr Biggers - Le Perroquet chinois, paru dans Ric et Rac, 1931-1932.djvu/147\">cette page est liée ou incluse dans d’autres pages</a>"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://en.wikipedia.org/wiki/Danilo_Gallinari","request_id":"a318a728-1b17-4635-931d-e502690104b2","id":"fe94c14e-dc5a-424a-9f8b-f085aa00bff2","dt":"2022-06-23T22:36:43Z","domain":"en.wikipedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949450},"id":1519050477,"type":"edit","namespace":0,"title":"Danilo Gallinari","comment":"","timestamp":1656023803,"user":"Urmomiscute","bot":false,"minor":false,"length":{"old":29203,"new":29243},"revision":{"old":1090822932,"new":1094673804},"server_url":"https://en.wikipedia.org","server_name":"en.wikipedia.org","server_script_path":"/w","wiki":"enwiki","parsedcomment":""}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://es.wikipedia.org/wiki/Nochebuena","request_id":"889ba0d7-22d4-4c75-9f57-7f7e1d64503b","id":"2d75dbb4-abe6-457e-88c8-f902ee5f16ef","dt":"2022-06-23T22:36:47Z","domain":"es.wikipedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949451},"id":268972493,"type":"edit","namespace":0,"title":"Nochebuena","comment":"Deshecha la edición 144370785 de [[Especial:Contribuciones/Charlyfar|Charlyfar]] ([[Usuario Discusión:Charlyfar|disc.]]) Sin referencias.","timestamp":1656023807,"user":"Eduardosalg","bot":false,"minor":false,"length":{"old":24222,"new":23943},"revision":{"old":144370785,"new":144370802},"server_url":"https://es.wikipedia.org","server_name":"es.wikipedia.org","server_script_path":"/w","wiki":"eswiki","parsedcomment":"Deshecha la edición 144370785 de <a href=\"/wiki/Especial:Contribuciones/Charlyfar\" title=\"Especial:Contribuciones/Charlyfar\">Charlyfar</a> (<a href=\"/wiki/Usuario_discusi%C3%B3n:Charlyfar\" title=\"Usuario discusión:Charlyfar\">disc.</a>) Sin referencias."}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://fr.wikipedia.org/wiki/G%C4%A7ar_Dalam","request_id":"72f134b9-66d6-4db8-a320-84fc853a8633","id":"fe241784-993d-446c-99b4-0dd917022d3d","dt":"2022-06-23T22:36:47Z","domain":"fr.wikipedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949452},"id":469519113,"type":"edit","namespace":0,"title":"Għar Dalam","comment":"","timestamp":1656023807,"user":"Lœnidra","bot":false,"minor":false,"patrolled":true,"length":{"old":3814,"new":3814},"revision":{"old":194790081,"new":194790084},"server_url":"https://fr.wikipedia.org","server_name":"fr.wikipedia.org","server_script_path":"/w","wiki":"frwiki","parsedcomment":""}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://commons.wikimedia.org/wiki/File:ISS064-E-55765_-_View_of_Earth.jpg","request_id":"06994d0b-ed3c-4320-9815-43435d926386","id":"de05b79a-b291-4186-8f26-8a9f6ce1991e","dt":"2022-06-23T22:36:47Z","domain":"commons.wikimedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949453},"id":1954751697,"type":"edit","namespace":6,"title":"File:ISS064-E-55765 - View of Earth.jpg","comment":"/* wbsetlabel-add:1|en */ View of Earth taken during ISS Expedition 64","timestamp":1656023807,"user":"AskeBot","bot":true,"minor":false,"patrolled":true,"length":{"old":495,"new":727},"revision":{"old":667834892,"new":667834895},"server_url":"https://commons.wikimedia.org","server_name":"commons.wikimedia.org","server_script_path":"/w","wiki":"commonswiki","parsedcomment":"‎<span dir=\"auto\"><span class=\"autocomment\">Added [en] caption: </span></span> View of Earth taken during ISS Expedition 64"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://commons.wikimedia.org/wiki/File:Brno-Bystrc,_Heyrovsk%C3%A9ho,_d%C4%9Btsk%C3%A9_h%C5%99i%C5%A1t%C4%9B_(17.25.06b).jpg","request_id":"7b29d3ca-d158-41de-8e2d-ba1e497f042e","id":"82ef2349-7550-46a7-a79d-c29f9f63ef6a","dt":"2022-06-23T22:36:47Z","domain":"commons.wikimedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949454},"id":1954751698,"type":"edit","namespace":6,"title":"File:Brno-Bystrc, Heyrovského, dětské hřiště (17.25.06b).jpg","comment":"/* wbsetclaim-create:2||1 */ [[d:Special:EntityPage/P180]]: [[d:Special:EntityPage/Q44659087]]","timestamp":1656023807,"user":"Bazi","bot":false,"minor":false,"patrolled":true,"length":{"old":639,"new":1074},"revision":{"old":667834454,"new":667834897},"server_url":"https://commons.wikimedia.org","server_name":"commons.wikimedia.org","server_script_path":"/w","wiki":"commonswiki","parsedcomment":"‎<span dir=\"auto\"><span class=\"autocomment\">Vytvořeno tvrzení: </span></span> <a href=\"https://www.wikidata.org/wiki/Special:EntityPage/P180\" class=\"extiw\" title=\"d:Special:EntityPage/P180\">d:Special:EntityPage/P180</a>: <a href=\"https://www.wikidata.org/wiki/Special:EntityPage/Q44659087\" class=\"extiw\" title=\"d:Special:EntityPage/Q44659087\">d:Special:EntityPage/Q44659087</a>"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://en.wikipedia.org/wiki/The_Human_Predicament","request_id":"37733e39-57eb-4b74-9148-e79d4e0b02cf","id":"41631326-9003-4ecc-b0cb-d33ab4a07c9d","dt":"2022-06-23T22:36:47Z","domain":"en.wikipedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949455},"id":1519050478,"type":"edit","namespace":0,"title":"The Human Predicament","comment":"added [[Category:Works about philosophical pessimism]] using [[WP:HC|HotCat]]","timestamp":1656023807,"user":"Yitzilitt","bot":false,"minor":false,"length":{"old":12769,"new":12818},"revision":{"old":1094673766,"new":1094673806},"server_url":"https://en.wikipedia.org","server_name":"en.wikipedia.org","server_script_path":"/w","wiki":"enwiki","parsedcomment":"added <a href=\"/wiki/Category:Works_about_philosophical_pessimism\" title=\"Category:Works about philosophical pessimism\">Category:Works about philosophical pessimism</a> using <a href=\"/wiki/Wikipedia:HC\" class=\"mw-redirect\" title=\"Wikipedia:HC\">HotCat</a>"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://www.wikidata.org/wiki/Q414257","request_id":"7355be8b-743f-4ec2-acbc-9680815fcf65","id":"73a617b0-400b-4c28-8c3a-a05f6eccee49","dt":"2022-06-23T22:36:47Z","domain":"www.wikidata.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949456},"id":1717965670,"type":"edit","namespace":0,"title":"Q414257","comment":"/* wbsetlabel-set:1|hu */ nepetalakton","timestamp":1656023807,"user":"Adam78","bot":false,"minor":false,"patrolled":true,"length":{"old":52346,"new":52346},"revision":{"old":1484976562,"new":1665827207},"server_url":"https://www.wikidata.org","server_name":"www.wikidata.org","server_script_path":"/w","wiki":"wikidatawiki","parsedcomment":"‎<span dir=\"auto\"><span class=\"autocomment\">[hu] címke módosítása: </span></span> nepetalakton"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://commons.wikimedia.org/wiki/File:Tecumseh_Township_locator,_Detroit,_2021.svg","request_id":"ddf4f677-c6d3-4a6d-9103-f205b409576f","id":"088d7d71-10bd-465a-b1b3-560efded0be3","dt":"2022-06-23T22:36:47Z","domain":"commons.wikimedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949457},"id":1954751699,"type":"edit","namespace":6,"title":"File:Tecumseh Township locator, Detroit, 2021.svg","comment":"/* wbeditentity-update:0| */ automatically modify [[Commons:Structured data|structured data]] based on file information: meta","timestamp":1656023807,"user":"SchlurcherBot","bot":true,"minor":false,"patrolled":true,"length":{"old":3347,"new":3703},"revision":{"old":571451013,"new":667834899},"server_url":"https://commons.wikimedia.org","server_name":"commons.wikimedia.org","server_script_path":"/w","wiki":"commonswiki","parsedcomment":"‎<span dir=\"auto\"><span class=\"autocomment\">Ein Objekt geändert: </span></span> automatically modify <a href=\"/wiki/Commons:Structured_data\" title=\"Commons:Structured data\">structured data</a> based on file information: meta"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://arz.wikipedia.org/wiki/%D9%81%D8%A7%D9%86%D9%89_(%D9%85%D8%BA%D9%86%D9%8A%D9%87_%D9%85%D9%86_%D9%81%D8%B1%D9%86%D8%B3%D8%A7)","request_id":"15ab2578-175e-40ac-b080-9a6c3795c658","id":"23d13b76-489b-4865-92e0-a5a66b45a47c","dt":"2022-06-23T22:36:47Z","domain":"arz.wikipedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949458},"id":38213366,"type":"new","namespace":0,"title":"فانى (مغنيه من فرنسا)","comment":"جديد","timestamp":1656023807,"user":"HitomiAkane","bot":false,"minor":false,"patrolled":true,"length":{"new":808},"revision":{"new":7067931},"server_url":"https://arz.wikipedia.org","server_name":"arz.wikipedia.org","server_script_path":"/w","wiki":"arzwiki","parsedcomment":"جديد"}
[okhttp-eventsource-events-[]-0] INFO WikimediaChangeHandler - {"$schema":"/mediawiki/recentchange/1.0.0","meta":{"uri":"https://commons.wikimedia.org/wiki/File:Canada_Geese_on_Dale_Dike_Reservoir_-_geograph.org.uk_-_3607586.jpg","request_id":"1bb74254-c5e9-4855-8768-980ccd11c2ee","id":"b14567f6-1eeb-4022-a07f-b9fb951c772b","dt":"2022-06-23T22:36:47Z","domain":"commons.wikimedia.org","stream":"mediawiki.recentchange","topic":"eqiad.mediawiki.recentchange","partition":0,"offset":3957949459},"id":1954751701,"type":"log","namespace":6,"title":"File:Canada Geese on Dale Dike Reservoir - geograph.org.uk - 3607586.jpg","comment":"Uploading geograph.org.uk image from https://www.geograph.org.uk/photo/3607586","timestamp":1656023807,"user":"GeographBot","bot":true,"log_id":326258374,"log_type":"upload","log_action":"upload","log_params":{"img_sha1":"0rtgwu9iagbx248kp84rlbvwgqyh3fy","img_timestamp":"20220623223647"},"log_action_comment":"uploaded &quot;[[File:Canada Geese on Dale Dike Reservoir - geograph.org.uk - 3607586.jpg]]&quot;: Uploading geograph.org.uk image from https://www.geograph.org.uk/photo/3607586","server_url":"https://commons.wikimedia.org","server_name":"commons.wikimedia.org","server_script_path":"/w","wiki":"commonswiki","parsedcomment":"Uploading geograph.org.uk image from https://www.geograph.org.uk/photo/3607586"}
```

# 60. Wikimedia Producer - Producer Config Intros
# 61. Producer Acknowledgements Deep Dive
Reconocimientos del productor o configuracion de reconocimiento.
## Producer Acknowledgements (acks)
Los productores pueden elegir recibir reconocimiento de datos al escribirlos y esto se configura en parametro de configuracion `acks`. Cuando este parametros es a:
* `acks=0`, el productor no espera por el reconocimiento de los datos escritos (Posible perdida de datos)
* `acks=1`, el productor espera por el reconocimiento de los datos escritos por la particion lider (Limitada perdida de datos)
* `acks=all` o `acks=-1`, el productor espera por el reconocimiento de los datos escritos por la particion lider y sus replicas (NO perdida de datos)

### Producer: `acks=all` and `min.insync.replicas`
`acks` en realidad va de la mano con otra configuracion `min.insync.replicas`, esto siginifica que la replica lider, cuando hace una escritura con `acks=all`, verifica si hay suficientes replicas sincronizadas en su cluster para escribir el mensaje de manera segura, y esto esta controlado por la configuracion `min.insync.replicas`.
* `min.insync.replicas=1`, acepta la escritura solo si el broker lider reconoce correctamente sus escrituras. 
* `min.insync.replicas=2`, (Recomendada, mayor garantia y seguridad) acepta la escritura si el broker lider y una replica reconoce correctamente sus escrituras. Sin embargo hay que tener en cuenta que si no existe una replica disponible ademas de la lider, kafka retornará una exception (NOT_ENOUGH-REPLICAS) diciendo que no tiene suficientes replicas para dar el OK de las escrituras.

## Kafka Topic Availability (Disponibilidad de temas de kafka)
Considerando un factor de replicacion 3

- Cuando `acks=0` y `acks=1`, si una particion esta arriba y considerada una replica sincronizada, el topico estara disponible para escritura.
- Cuando `acks=all` y:
  - Cuando `min.insync.replicas=1` (default), el topico debera tener al menos 1 particion disponible como replica sincronizada (Esto incluye a la lider), tolerando 2 brokers caidos.
  - Cuando `min.insync.replicas=2` (Recomendada), el topico debera estar disponible con al menos 2 replicas sincronizadas disponibles, tolerando 1 broker caido (Considerando el topico con factor de replicacion 3).
  - Cuando `min.insync.replicas=3`, cosiderando el factor de replicacion a 3, esto no tendria sentido, ya que no toleraria ningun broker caido.

# 62. Producer Retries (Reintentos en los productores)
En caso de algun fallo al momento de escribir un mensaje, se espera que los desarrolladores controlen las exceptions, de otra manera el mensaje se perderá.

Sin embargo existe un parametro de configuracion `retries` que controla lo reintentos que haria un productor automaticamente si existiese un error al momento de escribir un mensaje. Para las versiones de Kafka <= 2.0 el valor por defecto es `0`; para las versiones >= 2.1 el valor por defecto es `2147483647`.

Ademas existe otro parametro de configuracion  que trabaja en conjunto con el anterior `retry.backoff.ms`, controla el tiempo en milisegundos (Por defecto 100ms) entre un reintento y otro.

## Producer Timeouts
Desde la version 2.1 existe un parametro de configuracion para los timeouts `delivery.timeout.ms` (valor por defecto 120000ms, 2 minutos). Este tiempo de espera es el tiempo que se da al productor para que publique un mensaje.

# 63. Indempotent Producer

La propiedad que configurar un producto idempotente es `enable.idempotence` con valor `true`. Este parametro resuelve el siguiente escenario.

En una publicacion de un productor ideal sucederia:
1. El productor envia el mensaje a Kafka
2. Kafka escribe el mensaje.
3. Kafka responde al producto el reconocimiento de la escritura del mensaje.

Sin embargo puede suceder que en el paso 3, por algun motivo (error en la red), el productor no recibe el reconocimiento de escritura, y por la politica de reintentos, el productor vuelve a enviar a publicar el mensaje, por tanto, kafka volveria a escribir el mismo mensaje duplicado.

# 64. Safe Kafka Producer Settings
Los valores preterminados de un productor kafka y como tener un productor seguro.

- Desde kafka 3.0 el producto es seguro por defecto:
  - acks=all (-1)
  - enable.idempotence=true

* La configuracion para hacer a tu productor seguro es la siguiente:
  - `acks=all` (-1): Asegura que el producto reciba el reconocimiento de escritura de kafka
  - `min.insync.replicas=2`: Asegura que por lo menos en el lider y en una replica el mensaje quede escrito.
  - `enable.idempotence=true`: Asegura que kafka no escriba mensajes duplicados
  - `retries=MAX_INT`: reintenta hasta que se cumpla el tiempo de espera especificado en el parametro `delivery.timeout.ms`
  - `delivery.timeout.ms=120000`: al fallar un intento reintentará durante 2 minutos hasta publicar el mensaje.
  - `max.in.flight.requests.per.connection=5`: Asegura el maximo rendimiento mientras el mensaje mantiene el orden.

# 65. Wikimedia Producer Safe Producer Implementation

  Se agregan las propiedades especificadas en el punto anterior en la implementacion del producto en Java.

 # 66. Kafka Message Compression

  La compresion de mensaje puede darse a nivel productor, broker o topico.

## Message Compression at the Producer level (Compresion de mensaje en el productor)
El parametro para configuirar el tipo comprension es `compression.type`, pudiendo tomar los siguientes valores: none (default), gzip, lz4, snappy, zstd (Kafka 2.1).
La desventaja de tener compresion a nivel de productor, es que se obliga a los consumidores a la descompresion de los mensajes.
## Message Compression
* Considere probar `snappy` o `lz4` para una velocidad de compresion optima
* Considere ajustar `linger.ms` y `batch.size` para obligar a nuestros productores a tener lotes mas grandes y por tanto, tener mas compresion y rendimientos mas altos. 
## Message Compression at the Broker/Topic level
La compresion puede darse tambien a nivel de broker o a nivel topico. Cuando se da a nivel de broker esta compresion se aplica a todos los topicos y cuando se da a nivel de topico solo se aplica a ese topico. 

Existe un parametro de configuracion `compression.type` que segun su valor toma los siguiente comportamientos:
* `compression.type=producer`: responzabiliza al productor de comprimir el lote de mensajes y el broker toma este lote de mensajes y lo escribe tal cual sin volver a comprimirlos.
* `compression.type=none`: si el productor los comprime el broker los descomprime y despues escribe el lote de mensajes.
* `compression.type=lz4` (Por ejemplo): se especifica el algoritmo de compresion que quisieramos que usar del lado del broker. Sin embargo tener en cuenta que si el algoritmo de compresion es diferente al usado del lado del producto, kafka tendra que descomprimir y comprimir en base al algoritmo especificado, por ejemplo el lz4. 

RECOMENDACION: Dejar la configuracion por defecto `compression.type=producer` responzabilizando a los productores de hacer la compresion y asi no afectar el rendimiento del servidor de Kafka.

# 67. `linger.ms` and `batch.size` Producer settings
## ¿Como mejorar el mecanismo de procesamiento por lotes de Apache Kafka?

Un productor de kafka intentara publicar continuamente como le sea posible. Para esto existe un parametro de configuracion que le dice el maximo de publicaciones o solicitudes (request) que puede hacer por coneccion con el cluster de kafka `max.in.flight.requests.per.connection`, su valor recomendando es `5` (`max.in.flight.requests.per.connection=5`), es decir puede enviar 5 publicaciones en paralelo por coneccion.

Sin embargo puede darse el caso, en el que se cumple con el maximo de publicaciones al tiempo y sigue habiendo mensajes por publicar, es decir, hay publicaciones que deben hacerse mientras otras estan en vuelo. Kafka en estos casos es inteligente, ya que mientras existen publicaciones en vuelo, kafka va agrupando mensajes y arma lotes de mensajes para su proximo envio, entonces cada vez que todas sus conecciones esten ocupadas con el maximo de publicaciones en vuelo, kafka arma por lotes los mensajes.

El procesamiento por lotes inteligente ayuda a aumentar el rendimiento mientras mantiene una latencia muy baja.

Ademas del agrupamiento por lotes, existe un beneficio adicional al habilitar la COMPRESION de las publicaciones, haciendo mas compacto el lote a publicar. 

Existen dos parametros de configuracion que influyen en el mecanismo de procesamiento por lotes:
* `linger.ms` (0 por defecto): Es el tiempo de espera hasta que enviamos un lote a Kafka, este tiempo lo aprovecha el productor kafka para ir agregando mensajes al lote de mensajes que proximamente sera publicado.
* `batch.size` (16KB por defecto): Especifica el tamaño de los lotes de mensajes que arma el productor para su publcacion. Con esta propiedad aumentariamos el tamaño del lote y poder enviar la mayor cantidad de mensajes posibles. 

En conclusion asi debe lucir el codigo de un productor en Java:

```
properties.setProperty(COMPRESSION_TYPE_CONFIG, "snappy");
properties.setProperty(LINGER_MS_CONFIG, "20");
properties.setProperty(BATCH_SIZE_CONFIG, Integer.toString(32*1024));
```
# 68. Wikimedia Producer High Throughput Implementation

Se implementa en el codigo

# 69. Producer Default Partitioner & Sticky Partitioner

El particionador es un proceso que se encarga de elegir a que particion debe el productor enviar un lote de mensajes.

De la version 2.4 en adelante del cliente productor trae consigo el particionador `Sticky Partitioner` que es mucho mejor en rendimiento que el particionador que traia las versiones anteriores del cliente.

Se evidencia el particionador que usará al momento de ejecutar el productor y se una priopiedad asi:
```
partitioner.class = class org.apache.kafka.clients.producer.internals.DefaultPartitioner
```
Que al ver el codigo fuente de esta clase veras que tiene una dependencia con `org.apache.kafka.clients.producer.internals.StickyPartitionCache`:
```
package org.apache.kafka.clients.producer.internals;
...
public class DefaultPartitioner implements Partitioner {
	private final StickyPartitionCache stickyPartitionCache = new StickyPartitionCache();
...
}
```

# 70. [Advanced] max.block.ms and buffer.memory
Rara vez suele suceder que el productor tenga un rendimiento mas alto que lo que logra rendir el servidor kafka. Cuando esto sucede se bloquea el envio de mensajes al servidor por un tiempo configuirado en el parametro `max.block.ms`, y existe un buffer de memoria en el productor que almacena los lotes de mensajes que no se logran enviar por que el servidor Kafka esta demorado en responder lotes de mensajes previos. El tamaño de este buffer de memoria lo configuramos con el parametro `buffer.memory`.

