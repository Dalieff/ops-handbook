+++
title = "Lorem Ipsum"
layout = "home"
weight = 1
+++
<div style="display: flex; gap: 20px;">
  <!-- Карточка Cisco -->
  {{< cert-card 
      src="/images/linux-essentials.png" 
      title="Linux Essentials" 
      issuer="Cisco" 
      provider="Credly" 
      link="https://www.credly.com/badges/41b69b3a-e0c1-498f-81e1-70286a2423fb/public_url" >}}

  <!-- Карточка MS (теперь в таком же стиле) -->
  {{< cert-card 
      src="/images/ms.svg" 
      title="Cloud Concepts: Principles of Cloud Computing" 
      issuer="Microsoft" 
      provider="Microsoft" 
      link="https://learn.microsoft.com/api/achievements/share/ru-ru/MaksimDaliev-1614/HZJ9CY58?sharingId=DE82F1ACBC5D080F" >}}

 <!-- Карточка MongoDB -->
  {{< cert-card 
      src="/images/mongo.png" 
      title="MongoDB Overview: Core Concepts and Architecture" 
      issuer="MongoDB" 
      provider="Credly" 
      link="https://www.credly.com/badges/72ca8996-0702-44d6-be4f-3d68998e0b19/public_url" >}}
</div>