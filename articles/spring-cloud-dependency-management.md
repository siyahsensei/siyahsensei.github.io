# Spring Cloud Dependency Management
Bu makalenin genel konusu Spring Boot projelerindeki Spring Cloud bağımlılıklarının versiyonları nasıl yönetilebileceği üzerine olacak.

Spring Boot gün geçtikçe güncellenen ve yeni versiyonları çıkarılan bir framework. Bu yeni versiyonlar birçok yenilikle geliyor olsa da eski bağımlılıklar için tam bir uyumluluk garantisi vermiyor. Bundan dolayı en güncel versiyona güncelleyeyim de diyemiyorsunuz. Bunun başlıca sebebi, projenizde kullandığınız bağımlılıklar bu yeni versiyonu desteklemeyebiliyor. Onları da güncelleyeyim dediğiniz de hepsinin güncel versiyonu olmayabiliyor gibi gibi farklı durumlar oluşuyor.

Bu gibi problemlerden dolayı projelerimizin kullandığı bağımlılıkların versiyonları genelde yükseltilmez ve ilk oluşturduğumuz versiyon da kullanmaya devam ederiz.

## Projemize yeni bir bağımlılık eklersek
Bir çok bağımlılık kullanabiliyor olsak da Spring eko sistemi, bir geliştiricinin ihtiyaç duyabileceği ortak kalıpları hızlı ve güvenilir bir şekilde ulaşabileceği araçlar sağlar. Projelerimizde kullanmak için bir şeye ihtiyaç duyduğumuzda da ilk akla gelen çözüm Spring Cloud olacaktır. Fakat en başta belirttiğim problem burada baş gösteriyor. Hangi versiyonu nasıl seçeceğiz?

Bunun için de yine ortak bir çözüm düşünülmüş: **Spring Cloud Dependency Management**

## Uyumluluklar
Aşağıdaki tabloda Spring Boot versiyonuna göre hangi Spring Cloud versiyonu uyumlu onu görebiliriz.

![spring-cloud-version](https://raw.githubusercontent.com/karasensei/karasensei.github.io/main/articles/spring-cloud-version.png)

Spring Cloud versiyonları da geriye dönük uyumlu bir şekilde kendi içerisinde Hoxton.SR1, Hoxton.SR2 vb. gibi versiyonlar olarak bulunabiliyorlar. Fakat Spring Boot versiyonu için genel hatlar tablodaki gibidir. Tabloda bazı ufak kafa karıştırıcı şeyler var.

Bu karışıklıkların açıklamaları ise şöyle:

* Hoxton versiyonu için 2.2.x ve 2.3.x Spring Boot versiyonu uyumlu olması. Dikkat edilmesi gereken nokta 2.3.x için Hoxton.SR5 ve sonraki versiyonların kullanılması gerektiğidir.
* Dalston ömrünü doldurdu ve onun yerin Edware kullanılmaya başlanıldı. İkisi de 1.5.x versiyonu için kullanılabiliyor fakat kullanılması önerilen Edgware versiyonudur.
* Spring Boot 2.4.x veya 2.5.x için olan versiyon bilgisi ise bir kafa karışıklığı olmaması için Spring Cloud resmi sitesinde bulunduğu şekilde yazdım. Siz versiyonu yazarken 2020.0.x (örnek: 2020.0.0) şeklinde yazmanız yeterli olacaktır. Burada dikkat edilmesi gereken nokta ise 2.5.x versiyonu için 2020.0.3 ve sonrası olan versiyonlar seçilmelidir.

## Projeye Eklemek
Aşağıdaki gibi pom.xml içerisine tanımladığınızda **dependencyManagement** tagların arasındaki dependency versiyonuna göre dependencies içerisindeki bağımlılıkların versiyonunu belirliyor olacak.

```
<properties>
    <spring.cloud-version>Greenwich.SR5</spring.cloud-version>
</properties>
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring.cloud-version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-bus-amqp</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-config</artifactId>
    </dependency>
</dependencies>
```

Gradle için de aynı şekilde springCloudVersion a göre dependencies içerisindeki bağımlılıkların versiyonları belirlenecek.

```
buildscript {
  dependencies {
    classpath "io.spring.gradle:dependency-management-plugin:1.0.10.RELEASE"
  }
}

ext {
  set('springCloudVersion', "Greenwich.SR5")
}

apply plugin: "io.spring.dependency-management"

dependencyManagement {
  imports {
    mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
  }
}
dependencies {
  implementation 'org.springframework.cloud:spring-cloud-starter-config'
}
```

## Sonuç
Spring Cloud bağımsız projelerden oluşan bir araçlar topluluğudur. Bunların birbirinden bağımsız olarak geliştirilmesi ve bu versiyonların yönetilmesinde oluşan problemlerin yine kendi içinde bağımlılıkları grup halinde versiyon koyarak bir çözüm bulmuştur. Sizin Spring Boot versiyonunuza göre kullanmanız gereken Spring Cloud versiyonu belirleyip sorunsuz bir şekilde bağımlılıklarınızı yönetebilirsiniz.