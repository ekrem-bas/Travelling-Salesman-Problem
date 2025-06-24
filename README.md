# Gezgin Satıcı Problemi için Genetik Algoritma Çözümü

Bu proje, bilgisayar bilimlerinin NP-zor problemlerinden biri olan **Gezgin Satıcı Problemi'nin (Traveling Salesman Problem - TSP)** çözümü için **Genetik Algoritma (GA)** tabanlı bir yaklaşım sunmaktadır. Python'da geliştirilen bu uygulama, standart TSPLIB formatına benzer `.txt` dosyalarından problem verilerini okuyabilir, en iyi çözümü bulmak için GA hiperparametrelerini **Grid Search (Izgara Arama)** yöntemiyle sistematik olarak test edebilir ve sonuçları kapsamlı grafiklerle görselleştirebilir.

## Projenin Temel Özellikleri

* **Esnek Veri Okuma:** TSPLIB formatına benzer yapıdaki `.txt` dosyalarından şehir koordinatlarını okuyarak farklı problem setleriyle çalışabilme.
* **Gelişmiş Genetik Algoritma:** Etkili ve probleme özel olarak seçilmiş GA operatörleri (Turnuva Seçilimi, Sıralı Çaprazlama, Tersine Çevirme Mutasyonu).
* **Elitizm Stratejisi:** En iyi çözümlerin yeni nesillere doğrudan aktarılmasıyla yakınsama performansını artıran elitizm yaklaşımı.
* **Otomatik Parametre Optimizasyonu:** `population_size`, `crossover_rate`, `mutation_rate` gibi kritik hiperparametreler için en iyi kombinasyonu bulan sistematik bir Grid Search mekanizması.
* **Kapsamlı Görselleştirme:** Hem algoritmanın jenerasyonlar boyunca yakınsama performansını gösteren bir grafik, hem de bulunan en iyi rotanın şehirler üzerinde çizildiği bir ağ grafiğini tek bir ekranda sunma.

## Kurulum

Projeyi yerel makinenizde çalıştırmak için aşağıdaki adımları izleyin.

1.  **Projeyi Klonlayın:**
    ```bash
    git clone https://github.com/ekrem-bas/Travelling-Salesman-Problem.git
    cd Travelling-Salesman-Problem
    ```

2.  **Gerekli Kütüphaneleri Yükleyin:**
    Proje `matplotlib` ve `networkx` kütüphanelerini yoğun olarak kullanmaktadır. Aşağıdaki komut ile gerekli tüm kütüphaneleri kurabilirsiniz.
    ```bash
    pip install matplotlib networkx
    ```
    *Not: Proje bir Jupyter Notebook (`.ipynb`) dosyası olduğu için sisteminizde Jupyter'in de kurulu olması gerekmektedir (`pip install notebook`).*

## Nasıl Çalıştırılır?

1.  Proje klasöründe terminali açın ve Jupyter Notebook'u başlatın:
    ```bash
    jupyter notebook
    ```
2.  Tarayıcınızda açılan arayüzden `main.ipynb` dosyasına tıklayın.
3.  Notebook içerisindeki hücreleri sırasıyla çalıştırın (`Shift + Enter` veya "Run" butonu ile).
4.  Programın en sonundaki hücreyi çalıştırdığınızda, sizden bir TSP veri dosyasının yolunu girmeniz istenecektir. Örnek bir dosya adı girin:
    ```
    Lütfen TSP dosyasının yolunu girin (örn: tsp70.txt): tsp70.txt
    ```
5.  Program, tanımlanan tüm parametre kombinasyonlarını deneyerek en iyi sonucu bulacak ve sonunda en iyi ayarlar için oluşturulan görselleştirme grafiğini ekrana çizecektir.

---

## Proje Yapısı ve Algoritma Detayları

Bu proje, bir Genetik Algoritmanın tüm temel bileşenlerini modüler fonksiyonlar halinde implemente eder.

### Yardımcı Fonksiyonlar

* `calculate_distance(city1_coords, city2_coords)`: İki şehir arasındaki Öklid mesafesini hesaplar.
* `total_distance(tour, cities_coords)`: Bir turun (bireyin) toplam mesafesini hesaplar. Bu fonksiyon, algoritmanın **uygunluk fonksiyonu (fitness function)** olarak görev yapar.
* `read_tsp_file(file_path)`: Verilen yoldaki `.txt` dosyasını okuyarak şehir koordinatlarını bir sözlük yapısına aktarır.

### Temel Genetik Algoritma Bileşenleri

1.  **Kromozom Temsili ve Popülasyon:**
    * Bir çözüm (rota), şehirlerin sıralı bir listesi olarak **permutasyon tabanlı** bir kromozom ile temsil edilir.
    * `create_individual(num_cities)`: Geçerli ve rastgele bir rota (birey) oluşturur.
    * `create_initial_population(num_cities, population_size)`: Belirtilen boyutta bir başlangıç popülasyonunu rastgele bireylerle oluşturur.

2.  **Seçilim Mekanizması: Turnuva Seçilimi**
    * `tournament_selection(population, fitness_scores, k)`: Popülasyondan rastgele `k` adet birey seçer ve aralarından en iyi uygunluk değerine (en kısa mesafeye) sahip olanı ebeveyn olarak döndürür.

3.  **Çaprazlama Operatörü: Sıralı Çaprazlama (Order Crossover - OX1)**
    * `order_crossover_ox1(parent1, parent2)`: Permütasyon tabanlı problemler için özel olarak tasarlanmış bu operatör, iki ebeveynden genetik bilgiyi koruyarak geçerli yeni çocuklar (turlar) üretir.

4.  **Mutasyon Operatörü: Tersine Çevirme Mutasyonu (Inversion Mutation)**
    * `inversion_mutation(individual, mutation_rate)`: Bir bireyin rotası içindeki rastgele bir alt segmenti ters çevirerek genetik çeşitliliği artırır ve yerel optimumlardan kaçmaya yardımcı olur.

### Ana Algoritma ve Stratejiler

* `genetic_algorithm_tsp(...)`: Tüm GA bileşenlerini bir araya getiren ana fonksiyondur. Belirtilen jenerasyon sayısı boyunca evrimsel süreci yönetir.
* **Elitizm Stratejisi:** Bu fonksiyon içerisinde, her jenerasyonun en iyi bireyleri (`elite_rate` ile belirlenen oranda) bir sonraki jenerasyona doğrudan aktarılır. Bu, en iyi çözümlerin kaybolmasını önler.

### Parametre Optimizasyonu ve Deney Yürütme

* `parameter_tuning_experiment(cities_coords, problem_name)`: Projenin en güçlü özelliklerinden biridir. Bu fonksiyon, `param_grid` içinde tanımlanan farklı parametrelerin tüm olası kombinasyonlarını **Grid Search** yöntemiyle dener. Her kombinasyon için `genetic_algorithm_tsp` fonksiyonunu çalıştırır, sonuçları karşılaştırır ve en iyi performansı gösteren parametre setini bularak raporlar.

### Görselleştirme Araçları

* `plot_results_combined_animated(...)`: `matplotlib` ve `networkx` kütüphanelerini kullanarak sonuçları görselleştiren fonksiyondur. İki ana bileşenden oluşur:
    1.  **Yakınsama Grafiği:** Jenerasyonlar ilerledikçe en iyi mesafenin nasıl düştüğünü gösteren çizgi grafik.
    2.  **En İyi Tur Grafiği:** Bulunan en kısa rotayı, şehirler (düğümler) ve yollar (kenarlar) ile bir ağ grafiği olarak çizer.

## Kullanılan Kütüphaneler

* `math`: Matematiksel hesaplamalar için.
* `random`: Rastgelelik gerektiren tüm işlemler (popülasyon, seçim, mutasyon vb.) için.
* `matplotlib.pyplot`: Veri ve sonuç görselleştirmeleri için.
* `networkx`: Şehir ve rota ağını çizmek için.
* `time`: Algoritmanın çalışma süresini ölçmek için.
* `itertools`: Parametre optimizasyonunda kombinasyonları oluşturmak için.
