---
toc: true
layout: post
description: Implications of using public cloud services for processing personal data of EEA citizens.
categories: [schremsii, privacy, cloud, edpb]
title: Schrems II implications when using public cloud services
image: images/2021-02-schremsii/schremsii.jpg
hide: false
search_exclude: true
---

![]({{ site.baseurl}}/images/2021-02-schremsii/schremsii.jpg ){: height="350px" with="300px"}


This blog post addresses the impact of Schrems II in organisations within the European Economic Area (EEA) that host their services in public cloud services (owned by companies that are not part of EEA). In particular, I address the impact of EEA organisations that host their services in Microsoft Azure or Amazon Web Services (AWS). This analysis is non-exhaustive and the impact of Schrems II has more wider ramifications than the ones specifically addressed in this post.

*Please note that I'm not a person with legal background and this is just a summary of all the conclusions I've drawn over the past months during the discussions with my legal fellows about Schrems II. Therefore, this blog post mainly focus on the technical (security) measures.*


# What is Schrems II?
The Schrems II decision by the European Union Court of Justice invalidated the adequacy decision for the EU-U.S. Privacy Shield Framework in the mid of 2020. This framework was used to conduct transatlantic data transfers in compliance with European Union (EU) data protection rules ([GDPR](https://en.wikipedia.org/wiki/General_Data_Protection_Regulation)).
The Court required that companies should actively risk-assess whether supplementary measures (of technical nature) are needed for data transfers outside the EEA. After this assessment, if the company is still not able to "guarantee an essentially equivalent level of protection under EU law", then it should immediately suspend the transfer of personal data to the 3rd country.

# EDPB and recommendations on supplementary measures
In November of 2020, the European Data Protection Board (EDPB) released a list of recommendations for data transfers (to countries outside of EAA) in a way to help data controllers and processors to ensure compliance with GDPR (the document can be consulted [here](https://edpb.europa.eu/sites/default/files/consultation/edpb_recommendations_202001_supplementarymeasurestransferstools_en.pdf)). These recommendations contain a non-exhaustive list of examples of supplementary measures that can be used (by data processors and controllers) when transferring (personal) data cross-boarders. 
These supplementary measures can be of the following nature (1) contractual, (2) organisational or (3) technical.

All the measures can be combined in order to enhance the level of data protection. Nevertheless, EDPB states that contractual and organisational measures alone do not overcome access to personal data by public authorities of the 3rd country. In some cases, EDPB even considers that only technical measures can render ineffective access by public authorities in third countries to personal data technical.

***The recommendation of EDPB under Schrems II is the following:***
1. If the combined supplementary measures allow you to reach the level of protection in accordance to Article 46 GDPR, data transfers to the 3rd country may go ahead.
2. When the supplementary measures are not enough to reach the level of protection in accordance to Article 46 GDPR, then you must not start or (in case you are already conducting transfers) suspend (or end) the transfer of personal data to the 3rd country.

*Reference: paragraphs 50. and 51 of the EDPB document (see link above).*

# Supplementary measures of technical nature
EDPB gives a (non-exhaustive) list of scenarios describing supplementary measures of the technical nature and makes a distinction between scenarios where technical measures can be found and where they cannot. In this post I address some of the use cases described.

## Use case 1
*Data storage for backup and other purposes that do not require access to data in the clear.*

A data exporter uses a hosting service provider in a third country to store personal data, the following requirements should be in place:
1. Data must be encrypted using state of the art encryption algorithms that are considered to be secure and are flawlessly implemented and maintained;
2. The keys must be reliably managed and retained under the sole control of the data exporter or by a 3rd party that ensures an adequate level of protection;

### Example of a possible scenario for Use Case 1
Let's consider you have an application running in your private cloud and you wish to backup the (personal) data in some public cloud provider such as Azure or AWS (which are US-based companies and according to EDPB may not ensure the adequate level of protection as required in the GDPR).

Then all the data to be backed up must be encrypted using strong encryption and the keys must be stored in a place where the cloud providers cannot access it. For instance, if you're using Azure Storage or an AWS S3 bucket for the backup, the data must be encrypted before being copied and the keys cannot be stored in Key Vault (Azure) or KMS (AWS). The keys must be stored for instance, in a proper device in your private cloud (assuming your private cloud resides and is managed within the EEA).

## Use case 3 
*Encrypted data merely transiting third countries.*

A data exporter wishes to transfer data to a destination recognised as offering adequate protection in accordance with Article 45 GDPR. When the data is routed via a 3rd country that doesn't provide the adequate level of protection, then:
1. The data must be encrypted while in transit and should only be possible to decrypt it outside of the 3rd country in question;
2. Data exchange must rely on an agreed Public Key Infrastructure (PKI) using state of the art measures used against active and passive attacks;
3. Data encryptions must rely in state of the art algorithms that are flawlessly implemented and maintained;
4. Keys used for data encryption must be reliably managed.

### Example of a possible scenario for Use Case 3
Let's again consider you have an application A (that processes personal data) deployed in your private cloud service (hosted and managed in The Netherlands). Application A sends data to a 3rd party application B (hosted and managed in Germany) and the data is routed through the Azure cloud.

Then (as usual) you must protect the communication channel between application A and B using TLS (at least version 1.2) with an adequate cipher suite combination and using a trusted PKI service. The private keys must be reliably managed by you or your German counterpart, and must never be exposed to/in Microsoft Azure.

When these requirements are met, EDPB considers that the measures effectively provide the adequate level of protection of the personal data being transferred.

## Use case 6
*Transfer to cloud services providers or other processors which require access to data in the clear for their assigned tasks.*

In this case, EDPB considers that there are NO effective (technical) measures that can be employed to enforce the adequate level of protection. I guess this applies mainly when the cloud provider is owned and managed by a company from a 3rd country that does not provide the adequate level of protection.

### Example of a possible scenario for Use Case 6
Let's consider you have an application running in Azure or AWS that processes personal data of EU citizens and the cloud provider can access the data in the clear (e.g.: access to the data may be required to provide support when needed). Then you must suspend or end the data process (and remove the data), in case you don't have any other organisational or contractual measures in place that enable you to provide the adequate level of protection.

Please take into account that for processing any data in the cloud, you always need the data in the clear (processing encrypted data is not feasible for industry-like datasets). Since the cloud provider can access the data at any point in time, it may be compelled to handover your data to the authorities of the country where it operates, with or without your consent (and this is not compliant with the GDPR).

*It goes without saying, but please note that the translations of the use cases described in this post are non-exhaustive and are solely based on my interpretation (and past experience) of the EDPB guidelines.*

# Conclusion
If you own a company that processes personal data of EU citizens and hosts all its services in the public cloud providers, such as Azure or AWS, then you may need evaluate if the contractual or organisational measures in place are enough to you keep going.

As concluded by EDPB, at this point in time, there are no technical measures that can be easily implemented to continuing processing personal data in public cloud services as is, without the possibility of mass surveillance. Note that applications need to process cleartext data (we can't process data in its encrypted form yet), so if the environment where the data is processed is not under your total control, there's indeed the possibility of breach.

So in order to keep your services up and running, it seems that only the way forward (at this point in time) is for these public cloud services to create separate companies in the EEA that operate completely independently (legally and operationally) from the main corporate. Perhaps this is a bit premature conclusion, but as stated before this is just a preliminary analysis of the current scenarios.

---

**Acknowledgements**

I'd like to thank my colleague Ewoud Swart for the fruitful comments. His (legal) perspective did strengthen the content of this post.