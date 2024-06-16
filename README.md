# PRIMJENA YOLO ALGORITMA ZA PREPOZNAVANJE I ANALIZU PARKIRNIH MJESTA
## OSNOVNE INFORMACIJE
Ovaj projekt je dio mojeg završnog rada koji sam ja, David Kovačević, kao kandidat za prvostupnika [informatike](https://fipu.unipu.hr/) na [Sveučilištu Jurja Dobrile u Puli](https://www.unipu.hr/) izradio. Naziv završnog rada 
je "Primjena YOLO algoritma za prepoznavanje i analizu parkirnih mjesta", a glavnu korelaciju ima s kolegijem "Skladišta i rudarenje podataka". 
Mentor je doc. dr. sc. Goran Oreški, a komentor je Romeo Šajina, mag. inf.. 

U ovom radu istraživao sam primjenu YOLO (You Only Look Once) algoritma za prepoznavanje i
analizu parkirnih mjesta. Uvodni dio rada obuhvaća osnovne pojmove i metode detekcije objekata, te pregled dostupnih algoritama u ovom području. 
Odlučio sam se za YOLO algoritam zbog njegove učinkovitosti i preciznosti. 

U teorijskom dijelu rada opisao sam YOLO algoritam, uključujući njegovu arhitekturu 
i radni princip. Posebna pažnja posvećena je YOLOv8 verziji algoritma, koja predstavlja najnoviju iteraciju ovog pristupa. Korištene su različite varijante 
YOLOv8 algoritma, uključujući YOLOv8s, YOLOv8n i YOLOv8m s različitim parametrima treniranja. Implementacija je provedena na različitim YOLO modelima, 
koristeći pretrenirane modele na COCO skupu podataka, kao i modele trenirane od nule, ali svi su kasnije dodatno trenirani (fine-tuning) na specifičnom skupu podataka. 
Validacija i testiranje provedeni su kako bi se utvrdila kvaliteta svakog modela.

U ovom GitHub repozitoriji se nalazi skup podataka koji je korišten, YOLO modeli, kod za treniranje, validaciju i testiranje te sami rezultati navedenih procesa koji su spremljeni u novogeneirane direktorije.

## TEHNIČKE SPECIFIKACIJE
Implementacija YOLO algoritma zahtijeva pripremu okruženja kako bi se osiguralo učinkovito treniranje, validacija i testiranje modela. U ovom slučaju, lokalno okruženje VS code-a
je korišteno za izvođenje svih faza razvoja modela, čime je osigurana potpuna kontrola nad procesom i mogućnost optimizacije prema specifičnim zahtjevima projekta. Treniranje modela 
izvedeno je na laptopu s 16GB RAM-a, koristeći Ultralytics YOLOv8.2.22, Python 3.10.11, s CUDA 11.8 podrškom, uz NVIDIA GeForce RTX 3050 Ti Laptop GPU s 4096MiB memorije.

## SKUP PODATAKA
Izvor skupa podataka je [Muhammad Syihab, Parking Space Image Dataset](https://universe.roboflow.com/muhammad-syihab-bdynf/parking-space-ipm1b/dataset/4). 
Skup podataka se sastoji od tri direktorija: train, valid i test te od datoteka data.yaml, README.dataset i README.roboflow. README.dataset naglašava da skup 
podataka ima licencu [“CC BY 4.0 DEED”](https://creativecommons.org/licenses/by/4.0/) što omogućava da se slobodno može:
- dijeliti - kopirati i redistribuirati materijal u bilo kojem mediju ili formatu u bilo koju svrhu, čak i komercijalno.
- prilagoditi - remiksati, transformirati i nadograđivati materijal u bilo koju svrhu, čak i komercijalno.

Mora se dati odgovarajuće priznanje, pružiti poveznica na licencu i naznačiti jesu li napravljene izmjene. Naglašavam da je u sklopu izrade ovog završnog rada data.yaml izmijenjen te da su kroišteni
train, valid i test direktoriji koji su spremljeni u putanji datasets/Dataset.

U skupu podataka [Muhammad Syihab, Parking Space Image Dataset](https://universe.roboflow.com/muhammad-syihab-bdynf/parking-space-ipm1b/dataset/4) autor je primijenjenio sljedeće predobrade za slike te njhove oznake:
-	Automatska orijentacija pikselskih podataka (s uklanjanjem EXIF orijentacije)
-	Promjena veličine slike (engl. image size, skraćeno je imgsz) na 640x640 (proporcionalno rastezanje)
- Za stvaranje 3 verzije svake izvorne slike primijenjena je sljedeća augmentacija:
- Nasumično izrezivanje između 0 i 20 posto slike
- Nasumična rotacija između -20 i +20 stupnjeva
- Nasumična prilagodba svjetline između -25 i +25 posto
- Nasumična prilagodba ekspozicije između -10 i +10 posto
- Nasumično je primijenjeno Gaussovo zamućenje (engl. Gaussian blur) između 0 i 1 piksela
- Sol i papar šum primijenjen je na 2 posto piksela

Svaki direktoriji train, valid i test ima svoja dva direktorija zvana labels i images. Subdirektorij images ima slike u formatu jpg koje sadrže parkirna mjesta i vozila u slučaju da ih ima. 
Subdirektorij labels sadrži tekstualne datoteke u kojima svaki red ima numeričke vrijednosti koje označavaju danim redoslijedom: <klasa>, <x_centroid>, 
<y_centroid>, <širina> i <visina>.  <klasa> je indeks klase objekta (u ovom slučaju, "0" za slobodno parkirno mjesto i "1" za zauzeto parkirno mjesto). 
<x_centroid> i <y_centroid> su koordinate središta okvira za obuhvat (bounding box) normalizirane u rasponu od 0 do 1, u odnosu na širinu i visinu slike. 
<širina> i <visina> su normalizirane dimenzije okvira za obuhvat u odnosu na širinu i visinu slike. Iz dolje priložene slike za prvi red (1 0.3 0.303125 0.03203125 0.1) se može zaključiti:
-	1: Klasa (zauzeto parkirno mjesto).
-	0.3: Normalizirana x-koordinata središta okvira (30% širine slike).
-	0.303125: Normalizirana y-koordinata središta okvira (30.3125% visine slike).
-	0.03203125: Normalizirana širina okvira (3.203125% širine slike).
-	0.1: Normalizirana visina okvira (10% visine slike)

![image](https://github.com/Kovinejtor/Parking-Slot-Detection/assets/95875142/d4761703-0340-48de-80b5-fcd2c8685709)

Isti princip očitavanja vrijednosti vrijedi za ostale redove te ostale datoteke.

Važno je naglasiti da je autor skupa podataka odredio omjer i kvantitativnu distribuciju unutar skupa podataka [Muhammad Syihab, Parking Space Image Dataset](https://universe.roboflow.com/muhammad-syihab-bdynf/parking-space-ipm1b/dataset/4). 
Direktorij train sadrži 7017 slika, a samim time se može zaključiti da ima 7017 datoteka s oznakama za slike. Validacijski set sadrži 558 slika tj 558 datoteka s oznakama za slike, 
a set za testiranje sadrži 226 slika i 226 datoteka s oznakama. Omjer između broja slika tj oznaka je zadovoljavajući, a sveukupni broj slika je dovoljan za treniranje modela za potrebe ovog istraživanja. 

![image](https://github.com/Kovinejtor/Parking-Slot-Detection/assets/95875142/2da8085a-fc2e-4642-8fce-e3cf487d0bf6)

## IMPLEMENTACIJA YOLO ALGORITMA
U Parking_Slot_Detection.ipynb sam implementirao YOLO algoritam tj provedeni su treninzi, validacije i testiranja/predviđanja.
Modeli i parametri za pojedini trening:

![image](https://github.com/Kovinejtor/Parking-Slot-Detection/assets/95875142/601e70a6-d134-40be-8eca-8ad6f43ba454)

Prikazati ću kod samo za YOLOv8s s pretreniranim i bez pretreniranog modela, ali isti princip vrijedi i za ostale modele te parametre.
Unos potrebne biblioteke:
```
from ultralytics import YOLO
```

Stvaranje YOLOv8s pretreniranog modela koji je već istreniran na COCO skupu podataka:
```
model = YOLO('yolov8s.pt')
```

Druga opcija je stvaranje YOLOv8s modela koji nije pretreniran(tj od "nule"):
```
modelvs_empty = YOLO("yolov8s.yaml")
```

Treniranje modela:
```
model.train(data='datasets/Dataset/data.yaml', epochs=100, imgsz=640)
```

Validacija modela:
```
best_model_path = 'runs/detect/train/weights/best.pt'
best_model = YOLO(best_model_path)
metrics = best_model.val(data='datasets/Dataset/data.yaml', imgsz=640)
print(metrics)
```

Predviđanje/testiranje modela na testnom skupu podataka:
```
test_results = best_model.predict(source='datasets/Dataset/test/images', imgsz=640, conf=0.25, save=True, save_dir='output/predictions')
```

Nakon završetka treninga se u putanji runs/detect generira direktoriji "trainBroj". Nakon validacije se u putanji runs/detect generira "valBroj", a nakon testiranja se u putanji runs/detect generira "predictBroj".
Svaki od njih sadrži određene elemente koji su izlazni rezultati njihovog procesa kao npr model, krivulja i sl. Navesti ću koji direktoriji su vezani uz koji model i parametre:
- YOLOv8n bez pretreniranog modela: train7, val7, predict7
- YOLOv8s bez pretreniranog modela: train5, val5, predict5
- YOLOv8m bez pretreniranog modela i sa slikama 320×320: train6, val6, predict6
- YOLOv8n s pretreniranim modelom: train2, val2, predict2
- YOLOv8s s pretreniranim modelom: train, val, predict
- YOLOv8m s pretreniranim modelom i sa slikama 320×320: train3, val3, predict3
- YOLOv8m s pretreniranim modelom i sa slikama 480×480: train4, val4, predict4

## REZULTATI IMPLEMENTACIJE YOLO ALGORITMA
Za provjeru kvalitete različitih modela se treba koristiti testni skup koji su za model neviđeni podaci. YOLO nudi mogućnost da se s funkcijom predict prikažu te spreme izlazne slike 
s oznakama koje su rezultat predviđanja modela na neviđeni skup, ali prilikom toga nemamo nikakve izlazne evaluacijske metrike na temelju kojih možemo usporediti modele. 
U nastavku ću demonstrirati rezultate funkcije predict koristeći najbolji model, ali prvo ću prikazati način za prikaz evaluacijskih metrika testnog skupa.

U dolje priloženom kodu je moguće vidjeti da sam promijenio za validaciju putanju i to tako da joj putanja referencira testni skup.

```
train: C:/ParkingSlotDetection/datasets/Dataset/train/images
val: C:/ParkingSlotDetection/datasets/Dataset/test/images   #prilikom testiranja staviti /test/ kako bi se na neviđenim podacima provjerili modeli
test: C:/ParkingSlotDetection/datasets/Dataset/test/images

nc: 2
names: ['empty', 'occupied']
```


Izvest će validaciju (tj evaluaciju), ali na testnom skupu te će se saznati evaluacijske metrike. Na GitHub repozitoriju se nalazi nemodificirani data.yaml te u slučaju da čitatelj želi stvoriti evaluaciju za testni skup podataka onda 
treba samo promijeniti putanju za validaciju kao što je prikazano u gore priloženom kodu. Za prikaz evaluacijskih metrika je potrebno pokrenuti već postojeći kod za validaciju i to je potrebno napraviti za svaki dobiveni model.
Primjer postojećeg koda koji bi se ponovno trebao pokrenuti za evaluaciju testnog skupa:
```
best_model_path = 'runs/detect/train/weights/best.pt'
best_model = YOLO(best_model_path)
metrics = best_model.val(data='datasets/Dataset/data.yaml', imgsz=640)
print(metrics)
```

Broj instanci kod evaluacije testnog skupa podataka:
![image](https://github.com/Kovinejtor/Parking-Slot-Detection/assets/95875142/60812aad-5d75-49c6-8ca9-ab051b7442b9)

Broj slojeva, parametara i GFLOPs kod evaluacije testnog skupa podataka:
![image](https://github.com/Kovinejtor/Parking-Slot-Detection/assets/95875142/a59d6647-4b00-4368-8d5f-cdb328d2c835)

Vrijednosti odaziva kod evaluacije testnog skupa podataka:
![image](https://github.com/Kovinejtor/Parking-Slot-Detection/assets/95875142/928bb198-f9d8-4f65-b9e2-4e9c69bbf49c)

Vrijednosti mAP50 kod evaluacije testnog skupa podataka:
![image](https://github.com/Kovinejtor/Parking-Slot-Detection/assets/95875142/f1964fed-4219-4986-ba95-229d6e869eed)

Vrijednosti mAP50-95 kod evaluacije testnog skupa podataka:
![image](https://github.com/Kovinejtor/Parking-Slot-Detection/assets/95875142/9886c892-8630-4c3c-8ac8-800f36e25a18)

Izlaz funkcije predict je slika s predviđenim klasama i njihovim pozicijama. Primjer implementacije predikcije/testiranja:
```
test_results = best_model.predict(source='datasets/Dataset/test/images', imgsz=640, conf=0.25, save=True, save_dir='output/predictions')
```

Model YOLOv8m koji je treniran s pretreniranim modelom i s veličinom slika 480×480 je pokazao najbolji rezultat tijekom procesa treninga te validacije, 
ali i evaluacije s testnim skupom podataka što je bilo očekivano sukladno tome što je najveći te najpreciznniji model među korištenima. U nastavku su prikazane samo neke 
slike koje su rezultat predviđanja prethodno spomenutog, najboljeg modela, na testnom skupu podataka.

![image](https://github.com/Kovinejtor/Parking-Slot-Detection/assets/95875142/181c1dde-a172-4881-ab19-bd80875bb42f)

![image](https://github.com/Kovinejtor/Parking-Slot-Detection/assets/95875142/e3946834-7b30-4b67-a332-bd9aceb2b4f0)

![image](https://github.com/Kovinejtor/Parking-Slot-Detection/assets/95875142/c1de7c24-cd16-42db-baa6-3a0fb1ba81c0)

![image](https://github.com/Kovinejtor/Parking-Slot-Detection/assets/95875142/c0e3b36d-4b50-4d2e-a7a1-20c26b1b76d1)

![image](https://github.com/Kovinejtor/Parking-Slot-Detection/assets/95875142/a4e9fc7a-35e8-4e85-aab7-807dd69888b5)


## IZAZOVI I LIMITACIJE RADA
Važno je naglasiti da u skupu podataka ima kvalitetnih te manje kvalitetnih slika. Naglašavam da nije riječ o kvaliteti specifikacije kamere i slike već o samoj 
kvaliteti pozicioniranja kamera za izradu potrebne slike. Postoji dobar ugao i lošiji ugao te drugi apsekti pozicioniranja kamere. U određenim slikama model neće 
pronaći sva parkirna mjesta ne zato jer je model slab već zato jer je riječ o lošijem pozicioniranju slike što čini izazovnijim prepoznavanje parkirnih mjesta.

Korištenje naprednijeg hardvera s više GPU-ova ili s većom količinom RAM-a, omogućilo bi treniranje većih modela YOLOv8, kao što su YOLOv8l i YOLOv8x. Ovi modeli pružaju veću 
preciznost u prepoznavanju objekata, što bi moglo značajno poboljšati performanse sustava. Jedan od ključnih faktora za poboljšanje modela je korištenje većeg i raznovrsnijeg skupa podataka. 
Ovo će pomoći modelu da bolje generalizira i poveća njegovu robustnost u stvarnim situacijama.

## ZAKLJUČAK
Rezultati istraživanja primjene YOLOv8 algoritma za prepoznavanje i analizu parkirnih mjesta pokazali su da je model YOLOv8m s korištenjem pretreniranog modela, 100 epoha, 
veličinom slike 480, i batch veličine 16, dao najbolje rezultate. Visoka preciznost modela omogućava točnije informacije za korisnike, smanjujući frustracije uzrokovane netočnim 
podacima o slobodnim parkirnim mjestima. Ovo može povećati zadovoljstvo korisnika i efikasnost parkirnih sustava.  Iako je YOLOv8m postigao visoku preciznost, veći modeli poput
YOLOv8l ili YOLOv8x, uz adekvatan hardver, mogu potencijalno postići još veću preciznost. Prethodno navedeni model ima dobru preciznost i ostale izlazne evaluacijske parametre, 
ali trebalo bi se težiti da se za praktičnu primjenu koristi još precizniji, robusniji te kvalitetniji model.
