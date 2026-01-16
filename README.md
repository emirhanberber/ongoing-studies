import customtkinter as ctk
import sqlite3
from tkinter import messagebox

conn=sqlite3.connect("hakistok.hb")
cursor=conn.cursor()

cursor.execute("CREATE TABLE IF NOT EXISTS products(id INTEGER KEY AUTOINCREMENT,marka TEXT,kalite TEXT)")
cursor.execute("CREATE TABLE IF NOT EXISTS sizes(id INTEGER PRIMARY KEY AUTOINCREMENT,en INTEGER,boy INTEGER)")
cursor.execute("CREATE TABLE IF NOT EXISTS stock(product_id INTEGER,sizes_id INTEGER,adet INTEGER DEFAULT 0,PRIMARY KEY (product_id,size_id))")

sizes_list = [(200, 290),(170, 250),(160, 230),(120, 180),(100, 300),(100, 200),(80, 300),(80, 150)]
for en, boy in sizes_list:
    cursor.execute("SELECT id FROM sizes WHERE en=? AND boy=?", (en, boy))
    if not cursor.fetchone():
        cursor.execute("INSERT INTO sizes (en, boy) VALUES (?, ?)", (en, boy))
conn.commit()   #burada program ilk çalıştığında gerçekten altyapıda bir şey yok.o yüzden if not çalışıyor ve program sizes verilerini altyapıya ekliyor.program ikinci üçüncü kez çalıştığında soruyor bu sizes var mı yok mu diye böylece tekrar tekrar altyapıya eklenmesini engelliyor


class ProductFrame(ctk.CTkFrame): #bu oluşturduğu kutu şudur marka,kalite isimlerinin sil butonu olan içinde numaraları tutacak olan framedir
    def __init__(self, master, product_id, marka, kalite, **kwargs): #burası ilk bu frame çalıştığında çalışacak ilk kısımdır.self bu kutu kendisine aitdir ve bu terimlerde başına self geldimi kutunun kendisine ait olacağını belirtir
        super().__init__(master, **kwargs) #burası diyorki benim ne haddime kutu oluşturmak sen üst makam yani kutuyu oluştur (ctk.CTkFrame) den bahsediyorum.ben ise ona kendim bazı nacizane bir kaç özellik ekleyeceğim onları buradan eklerim
        self.product_id = product_id #birazdan + butonuna bastığımızda,kutunun içindeki id numarasını arttırsın diye bunu buraya tanımladık
        self.configure(border_width=2, border_color="#3a7ebf")

        header = ctk.CTkFrame(self, fg_color="#3a7ebf", height=30)
        header.pack(fill="x") #buradaki self var ya işte o anlatıyor ana frame in elemanı olduğunu onun üstüne geleceğini.ve ilk olarak pack yapıyını burada kullandığımız kutunun (YANİ FRAME nin başına) bu gelri.fill doldur manasına gelir.fill=x x yönünde ,fill=y y yönünde,fill=both ise her iki yöndede doldur demektir
        
        info_label = ctk.CTkLabel(header, text=f"{marka.upper()} - {kalite.upper()}", text_color="white", font=("Arial", 13, "bold"))
        info_label.pack(side="left", padx=10)

        del_btn = ctk.CTkButton(header, text="SİL", width=40, height=20, fg_color="#c0392b", hover_color="#e74c3c", command=self.delete_product)
        del_btn.pack(side="right", padx=5)
