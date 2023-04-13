# Created SQLite database using by Python

Bu bölümde python ile ORM tekniği ile SQLite veritabanı oluşturmayı öğreneceğiz. Bu kdoda veritabanı ile bağlantı kurmak için DBeaver kullanılmıştır.

Peki neden SQLite? SQLite daha çok yerel olarak veri depolamak ve işlemek için kullanılır, bilgisayarda bir dosya açar ve bu dosya üzerinde işlemler gerçekleştirir.
DBeaver ve Python dili ile doğrudan SQL komutu göndermek ile ORM kullanmak bir noktada önemli bir farklılık içermektedir. DBeaver ve Python ile doğrudan veri tabanına erişilebilir ve komut gönderilerek uygulanabilir. Ancak ORM, nesne yönelimli bir programın veri tabanı tasarımıyla eşleştirilmesi ile oluşturulur ve ilgili programlamada yapılan güncellemeler veri tabanına yansıtılır. Bu sebeple bir veri tabanı güncellemesi yapmak için halihazırda Python dilinde bir program yazılmalı ve bu program üzerinde yapılacak güncellemeler ile veri tabanı güncellemeleri gerçekleştirilmelidir.

ORM tekniğini kullanabilmek için öncelikle SQLAlchemy paketini kurmamız gerekiyor:

```
pip instal sqlalchemy
```
Şimdi SQLite kullanmak için sqlite3 paketini içe aktaralım:

```
import sqlite3
```

SQLAlchemy’i, ilgili alt paketleriyle birlikte çağıralım:
```
import sqlalchemy
from sqlalchemy import create_engine, Column, Integer, String, Date, ForeignKey
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import relationship
```
Veri tabanı motoru oluşturalım ve veritabanı ile bağlantı sağlayalım. Burada dikkat etmemiz gereken şey python dosyanız ile veritabanı dosyası aynı klasör içerisinde bulunmalıdır:

```
engine = create_engine("sqlite:///db")
Base = declarative_base()
```
Yukarıda ```declarative_base()``` fonksiyonu kullanım amacı bildirime dayalı eşleme sürecini kendisinden türetilen tüm alt sınıflara uygulayacak olan işlevi kullanarak bir temel sınıf oluşturmaktır.

Şimdi oluşturmak istediğimiz veritabanı yapısını tanıyalım. Aşağıdaki görselde oluşturulması gereken sınıflar ve birbirleri ile ilişkileri gösterilmiştir.

![image](https://user-images.githubusercontent.com/74383996/231750849-b6a48d67-af11-48de-962d-8c0ab0e14c73.png)

ORM yapısında veri tabanındaki bir tablo, nesne yönelimli programlamada bir Class (sınıf) ile eşleşmektedir. Bu sebeple veri tabanı üzerinde herhangi bir işlem gerçekleştirmeden önce bu işlemin yapılacağı sınıfın tanımlanmış olması gerekmektedir.

Örnek olarak Yoneticilik sınıfını oluşturalım ve veri tabanı üzerinde eşleştirme yapalım:
```
class Yoneticilik(Base):
    #__tablename__ değişkeni, bu sınıfın hangi tablo ile birlikte çalıştığını tanımlar
    __tablename__ = "Yoneticilik"
    id = Column(Integer, primary_key=True)
    calisan_id = Column(Integer, ForeignKey("Calisan.id"))
    bolum_id = Column(Integer, ForeignKey("Bolum.id"))
    baslangicTarihi = Column(Date)
    bitisTarihi = Column(Date)
    
    calisan = relationship("Calisan", back_populates="yoneticilik")
    bolum = relationship("Bolum", back_populates="yoneticilik")
```
```
#Bu kod bloğu çalıştırıldığında tanımlanan sınıflar veri tabanı üzerinde de oluşturulur ve eşleştirmeler tamamlanır
Base.metadata.create_all(engine)
```

