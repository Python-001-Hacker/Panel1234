
import telebot
import requests
import os
import requests
from bs4 import BeautifulSoup
import telebot

TOKEN = "sil token ekle "
bot = telebot.TeleBot(TOKEN)

@bot.message_handler(commands=['start'])
def botu_baslatma(message):
    bot.reply_to(message, "@TSG_YunusOfficial Sorgu Bot'una Hoş geldiniz! Komutlar için /komut Yazın.")

@bot.message_handler(commands=['komut'])
def send_help_message(message):
    bot.reply_to(message, """
/adres 🏠 komutu ile TC'den Adres Bulabilirsin.
/tc 🧖‍♂ komutu kullanarak Kişi Bilgi Alabilirsin.
/sorgu 🇹🇷 komutu ile -Ad -Soyad -İl Sorgulama Yapar.
/aile 👨‍👩‍👦 komutu kullanarak aile sorgulama yapar.
/ip 🌐 ile ip sorgu yapabilirsiniz.
/gsmtc 📱 GSM DEN TC VERİR
/iban [İBAN BİLGİ VERİR ] 
/gsm 📱 NUMARDAN TC VERİR
/apartman KİSİ VE ORAD AOTURAN BİLGİ VERİR
/sulale 7 CEDDİ VERİRİ
/slalgsm 7 CEDDİ GSM VERİR
/sicil sicil bilgisi verir


    """)

@bot.message_handler(commands=['adres'])
def adress_sorgu(message):
    try:
        chat_id = message.chat.id
        tc_number = message.text.split()[1]  

        
        url = f"https://api.tsgonline.net/tsgucretsizapi/adres.php?tc={tc_number}"
        response = requests.get(url)
        data = response.json()

        if "success" in data and data["success"]:
            adres_data = data["data"]
            
            
            adres_message = f"""
╭━━━━━━━━━━━━━━
┃➥ Kimlik No: {adres_data["TC"]}
┃➥ Ad Soyad: {adres_data["ADSOYAD"]}
┃➥ Doğum Yeri: {adres_data["DOGUMYERI"]}
┃➥ Vergi Numarası: {adres_data["VERGINO"]}
┃➥ İkametgah: {adres_data["ADRES"]}
╰━━━━━━━━━━━━━━
"""
            bot.reply_to(message, adres_message)  
        else:
            bot.reply_to(message, "Bu TC numarasına ait adres bilgisi bulunamadı.")  
    except IndexError:
        bot.reply_to(message, "Geçersiz komut. Kullanım: /adres 12345678901")  
    except Exception as e:
        print(f"Adres sorgulama hatası: {str(e)}")  
        bot.reply_to(message, "Bir hata oluştu.")  

@bot.message_handler(commands=['tc'])
def adress_sorgu(message):
    try:
        chat_id = message.chat.id
        tc_number = message.text.split()[1]

        url = f"https://api.tsgonline.net/tsgucretsizapi/adpro.php?auth=tsgxyunus&tc={tc_number}"
        response = requests.get(url)
        data = response.json()

        print(data)

        if "success" in data and data["success"]:
            adres_data = data["data"][0]
            
            adres_message = f"""
╭━━━━━━━━━━━━━━━━━━━━━
┃➥ Kimlik No: {adres_data["TC"]}
┃➥ Ad: {adres_data["AD"]}
┃➥ Soyad: {adres_data["SOYAD"]}
┃➥ Doğum Yeri: {adres_data["DOGUMYERI"]}
┃➥ Doğum Tarihi: {adres_data["DOGUMTARIHI"]}
┃➥ Baba Adı: {adres_data["BABAADI"]}
┃➥ Anne Adı: {adres_data["ANNEADI"]}
┃➥ Medeni Hali: {adres_data["MEDENIHAL"]}
┃➥ Cinsiyet: {adres_data["CINSIYET"]}
┃➥ GSM: {adres_data["GSM"]}
┃➥ Memleket İl: {adres_data["MEMLEKETIL"]}
┃➥ Memleket İlçe: {adres_data["MEMLEKETILCE"]}
┃➥ Memleket Köy: {adres_data["MEMLEKETKOY"]}
┃➥ Adres İl: {adres_data["ADRESIL"]}
┃➥ Adres İlçe: {adres_data["ADRESILCE"]}
╰━━━━━━━━━━━━━━━━━━━━━
"""
            bot.reply_to(message, adres_message)
        else:
            bot.reply_to(message, "Bu TC numarasına ait veri bulunamadı.")
    except IndexError:
        bot.reply_to(message, "Geçersiz komut. Kullanım: /tc 12345678901")
    except Exception as e:
        print(f"TC sorgulama hatası: {str(e)}")
        bot.reply_to(message, "Bir hata oluştu.")

@bot.message_handler(commands=['sorgu'])
def kimlik_sorgu(message):
    try:
        chat_id = message.chat.id
        parameters = ' '.join(message.text.split()[1:]).split('-')[1:]
        query = {}
        for param in parameters:
            key_value = param.split()
            if len(key_value) == 2:
                key, value = key_value
                key = key.strip().lower()
                query[key] = value.strip()

        if query and 'ad' in query and 'soyad' in query and 'il' in query:
            url = f"https://api.tsgonline.net/tsgucretsizapi/adpro.php?auth=tsgxyunus&" + '&'.join([f'{key}={value}' for key, value in query.items()])
            response = requests.get(url)
            data = response.json()

            if "success" in data and data["success"]:
                person_info = ""
                for person_data in data["data"]:
                    person_info += f"""
╭━━━━━━━━━━━━━━━━━━━━━
┃➥ TC: {person_data["TC"]}
┃➥ Ad: {person_data["AD"]}
┃➥ Soyad: {person_data["SOYAD"]}
┃➥ Baba Adı: {person_data["BABAADI"]}
┃➥ Anne Adı: {person_data["ANNEADI"]}
┃➥ Doğum Tarihi: {person_data["DOGUMTARIHI"]}
┃➥ Doğum Yeri: {person_data["DOGUMYERI"]}
┃➥ Memleket İl: {person_data["MEMLEKETIL"]}
┃➥ Adres İl: {person_data["ADRESIL"]}
┃➥ Medeni Hali: {person_data["MEDENIHAL"]}
┃➥ Cinsiyet: {person_data["CINSIYET"]}
╰━━━━━━━━━━━━━━━━━━━━━

"""

                
                file_name = f"person_info_{chat_id}.txt"
                with open(file_name, 'w', encoding='utf-8') as file:
                    file.write(person_info.strip())
                
                with open(file_name, 'rb') as file:
                    bot.send_document(chat_id, file)

                os.remove(file_name)
            else:
                bot.reply_to(message, "Böyle bir kişi bilgisi bulunamadı.")
        else:
            bot.reply_to(message, "Geçersiz komut. Kullanım: /sorgu -ad Adnan -soyad Oktar -il Diyarbakır")
    except IndexError:
        bot.reply_to(message, "Geçersiz komut kullanım. Örnek: /sorgu -ad Adnan -soyad Oktar -il Diyarbakır")
    except Exception as e:
        print(f"Kimlik sorgulama hatası: {str(e)}")
        bot.reply_to(message, "Bir hata oluştu.")

@bot.message_handler(commands=['aile'])
def aile_sorgu(message):
    try:
        chat_id = message.chat.id
        tc_number = message.text.split()[1]

        
        url = f"https://api.tsgonline.net/tsgucretsizapi/aile.php?tc={tc_number}"
        response = requests.get(url)
        data = response.json()

        if "success" in data and data["success"]:
            family_info = ""

            for member_data in data["data"]:
                
                family_info += "╭━━━━━━━━━━━━━━\n"
                for key, value in member_data.items():
                    family_info += f"┃➥ {key.capitalize()}: {value}\n"
                family_info += "╰━━━━━━━━━━━━━━\n\n"  
            file_name = f"family_info_{chat_id}.txt"
            with open(file_name, 'w', encoding='utf-8') as file:
                file.write(family_info)

            
            with open(file_name, 'rb') as file:
                bot.send_document(chat_id, file)

            
            os.remove(file_name)
        else:
            bot.reply_to(message, "Aile bilgisi bulunamadı.")
    except IndexError:
        bot.reply_to(message, "Geçersiz komut kullanım: /aile 12345678901")
    except Exception as e:
        bot.reply_to(message, "Bir hata oluştu.")



@bot.message_handler(commands=['iban'])
def iban_sorgu(message):
    try:
        chat_id = message.chat.id
        iban = message.text.split()[1]

        cookies = {
            'PHPSESSID': 'jthkuejr3j9f6jetegjnfp1ou2',
            '_ga': 'GA1.1.1031110533.1731185638',
            '_ga_HMMSM1LX9C': 'GS1.1.1731185638.1.0.1731185650.48.0.0',
        }

        headers = {
            'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7',
            'Accept-Language': 'tr-TR,tr;q=0.9,en-US;q=0.8,en;q=0.7',
            'Cache-Control': 'max-age=0',
            'Connection': 'keep-alive',
            'Content-Type': 'application/x-www-form-urlencoded',
            'Origin': 'https://hesapno.com',
            'Referer': 'https://hesapno.com/mod_iban_coz',
            'Sec-Fetch-Dest': 'document',
            'Sec-Fetch-Mode': 'navigate',
            'Sec-Fetch-Site': 'same-origin',
            'Sec-Fetch-User': '?1',
            'Upgrade-Insecure-Requests': '1',
            'User-Agent': 'Mozilla/5.0 (Linux; Android 10; K) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/124.0.0.0 Mobile Safari/537.36',
            'sec-ch-ua': '"Not-A.Brand";v="99", "Chromium";v="124"',
            'sec-ch-ua-mobile': '?1',
            'sec-ch-ua-platform': '"Android"',
        }

        data = {
            'iban': iban,
            'x': '84',
            'y': '29',
        }

        response = requests.post('https://hesapno.com/mod_coz_iban.php', cookies=cookies, headers=headers, data=data)
        goku = BeautifulSoup(response.text, 'html.parser')

        def godlesstekno(tag, varsayilan=''):
            return tag.next_sibling.strip() if tag and tag.next_sibling else varsayilan

        result = {
            'Banka Adı': godlesstekno(goku.find('b', string='Ad:')),
            'Banka Kodu': godlesstekno(goku.find('b', string='Kod:')),
            'Swift': godlesstekno(goku.find('b', string='Swift:')),
            'Hesap No': godlesstekno(goku.find('b', string='Hesap No:')),
            'Şube Adı': godlesstekno(goku.find_all('b', string='Ad:')[1]) if len(goku.find_all('b', string='Ad:')) > 1 else '',
            'Şube Kodu': godlesstekno(goku.find_all('b', string='Kod:')[1]) if len(goku.find_all('b', string='Kod:')) > 1 else '',
            'İl': godlesstekno(goku.find('b', string='İl:')),
            'İlçe': godlesstekno(goku.find('b', string='İlçe:')),
            'Tel': godlesstekno(goku.find('b', string='Tel:')),
            'Fax': godlesstekno(goku.find('b', string='Fax:')),
            'Adres': godlesstekno(goku.find('b', string='Adres:')),
        }

        iban_info = "╭━━━━━━━━━━━━━━\n"
        for key, value in result.items():
            iban_info += f"┃➥ {key}: {value}\n"
        iban_info += "╰━━━━━━━━━━━━━━"

        bot.reply_to(message, iban_info)

    except IndexError:
        bot.reply_to(message, "Geçersiz komut kullanımı. Örnek: /iban TR******")
    except Exception as e:
        print(f"IBAN sorgulama hatası: {str(e)}")  
        bot.reply_to(message, "Bir hata oluştu.")



@bot.message_handler(commands=['gsm'])
def handle_gsm(message):
    try:
        gsm_numarasi = message.text.split()[1]
        url = f'https://api.tsgonline.net/tsgucretsizapi/gsmtc.php?gsm={gsm_numarasi}'
        response = requests.get(url)
        data = response.json()

        if "success" in data and data["success"]:
            if "data" in data and len(data["data"]) > 0:
                tablo = "╭━━━━━━━━━━━━━━\n"
                for detay in data["data"]:
                    tablo += f"""┃➥ TC: {detay['TC']}
┃➥ GSM: {detay['GSM']}\n"""
                tablo += "╰━━━━━━━━━━━━━━"
                bot.reply_to(message, tablo)
            else:
                bot.reply_to(message, "GSM sorgusu başarılı, ancak bilgi bulunamadı.")
        else:
            bot.reply_to(message, "Bu GSM numarasına ait bilgi bulunamadı.")
    
    except IndexError:
        bot.reply_to(message, "Bir GSM numarası girmelisiniz. Örnek kullanım: /gsm 5326112849")
    except Exception as e:
        bot.reply_to(message, f"GSM sorgulanırken bir hata oluştu: {str(e)}")
        
@bot.message_handler(commands=['tcgsm'])
def handle_tcgsm(message):
    try:
        tc_numarasi = message.text.split()[1]
        url = f'https://api.tsgonline.net/tsgucretsizapi/tcgsm.php?tc={tc_numarasi}'
        response = requests.get(url)
        data = response.json()

        if "success" in data and data["success"]:
            if "data" in data and len(data["data"]) > 0:
                tablo = "╭━━━━━━━━━━━━━━\n"
                for detay in data["data"]:
                    tablo += f"""┃➥ TC: {detay['TC']}
┃➥ GSM: {detay['GSM']}\n"""
                tablo += "╰━━━━━━━━━━━━━━"
                bot.reply_to(message, tablo)
            else:
                bot.reply_to(message, "TC GSM sorgusu başarılı, ancak bilgi bulunamadı.")
        else:
            bot.reply_to(message, "Bu TC numarasına ait bilgi bulunamadı.")
    
    except IndexError:
        bot.reply_to(message, "Bir TC numarası girmelisiniz. Örnek kullanım: /tcgsm 11111111110")
    except Exception as e:
        bot.reply_to(message, f"TC GSM sorgulanırken bir hata oluştu: {str(e)}")
        
@bot.message_handler(commands=['apartman'])
def apartman_sorgu(message):
    try:
        chat_id = message.chat.id
        tc_number = message.text.split()[1]

        url = f"https://api.tsgonline.net/tsgucretsizapi/apartman.php?tc={tc_number}"
        response = requests.get(url)
        data = response.json()

        if "success" in data and data["success"]:
            apartman_info = "╭━━━━━━━━━━━━━━\n"
            apartman_info += f"┃➥ TC: {data['data']['TC']}\n"
            apartman_info += f"┃➥ Ad Soyad: {data['data']['ADSOYAD']}\n"
            apartman_info += f"┃➥ Doğum Yeri: {data['data']['DOGUMYERI']}\n"
            apartman_info += f"┃➥ Vergi No: {data['data']['VERGINO']}\n"
            apartman_info += f"┃➥ Adres: {data['data']['ADRES']}\n"
            apartman_info += "╰━━━━━━━━━━━━━━\n\n"

            apartman_info += "Apartmandaki Diğer Kişiler:\n"
            apartman_info += "╭━━━━━━━━━━━━━━\n"
            for kisi in data["data"]["Apartmandakiler"]:
                apartman_info += f"┃➥ TC: {kisi['TC']}\n"
                apartman_info += f"┃➥ Ad Soyad: {kisi['ADSOYAD']}\n"
                apartman_info += f"┃➥ Doğum Yeri: {kisi['DOGUMYERI']}\n"
                apartman_info += f"┃➥ Vergi No: {kisi['VERGINO']}\n"
                apartman_info += f"┃➥ Adres: {kisi['ADRES']}\n"
                apartman_info += "╰━━━━━━━━━━━━━━\n\n"

            file_name = f"apartman_info_{chat_id}.txt"
            with open(file_name, 'w', encoding='utf-8') as file:
                file.write(apartman_info)

            with open(file_name, 'rb') as file:
                bot.send_document(chat_id, file)

            os.remove(file_name)
        else:
            bot.reply_to(message, "Apartman bilgisi bulunamadı.")

    except IndexError:
        bot.reply_to(message, "Geçersiz komut kullanım: /apartman 12345678901")
    except Exception as e:
        bot.reply_to(message, f"Bir hata oluştu: {str(e)}")
        
@bot.message_handler(commands=['ip'])
def ip(message):
    if message.chat.type != "private":
        return

    chat_id = message.chat.id


    if len(message.text.split(' ')) < 2:
        bot.send_message(chat_id, "Lütfen bir IP adresi belirtin.")
        return

    ip = message.text.split(' ')[1]

    api_url = f'http://ip-api.com/json/{ip}'
    response = requests.get(api_url)

    if response.status_code == 200:
        data = response.json()
        if data["status"] == "success":
            response_message = f"╭━━━━━━━━━━━━━╮\n" \
                              f"┃➥ ÜLKE: {data['country']}\n" \
                              f"┃➥ ÜLKE KODU: {data['countryCode']}\n" \
                              f"┃➥ BÖLGE: {data['regionName']}\n" \
                              f"┃➥ BÖLGE ADI: {data['city']}\n" \
                              f"┃➥ ZIP KOD: {data['zip']}\n" \
                              f"┃➥ ENLEM: {data['lat']}\n" \
                              f"┃➥ BOYLAM: {data['lon']}\n" \
                              f"┃➥ SAAT DİLİMİ: {data['timezone']}\n" \
                              f"┃➥ İSP: {data['isp']}\n" \
                              f"╰━━━━━━━━━━━━━╯\n\n"
            bot.send_message(chat_id, response_message, parse_mode="Markdown")
        else:
            bot.send_message(chat_id, "IP adresi bulunamadı.")
    else:   
        bot.send_message(chat_id, "API SORUNLU 😭.")  
        
@bot.message_handler(commands=['sulale'])
def sulale(msg):
    
    tc_number = msg.text.split()[1] if len(msg.text.split()) > 1 else None

    if tc_number:
        
        url = f"https://api.tsgmods.com.tr/t5a12/sulale.php?auth=tsgxyunus&tc={tc_number}"
        response = requests.get(url)
        data = response.json()

        if data['success'] == 'true':
            
            filename = f"sulale_{tc_number}.txt"
            with open(filename, 'w', encoding='utf-8') as f:
                
                kendisi = data['kendisi'][0]
                f.write(f"Kişi Bilgileri:\n\n")
                f.write(f"╭━━━━━━━━━━━━━━\n")
                f.write(f"┃➥ TC: {tc_number}\n")
                f.write(f"┃➥ AD: {kendisi['AD']}\n")
                f.write(f"┃➥ SOYAD: {kendisi['SOYAD']}\n")
                f.write(f"┃➥ DOĞUM TARİHİ: {kendisi['DOGUMTARIHI']}\n")
                f.write(f"┃➥ MEMLEKET İL: {kendisi['MEMLEKETIL']}\n")
                f.write(f"┃➥ MEMLEKET İLÇE: {kendisi['MEMLEKETILCE']}\n")
                f.write(f"┃➥ GSM: {kendisi['GSM']}\n")
                f.write(f"╰━━━━━━━━━━━━━━\n\n")

                
                annesi = data['annesi'][0]
                f.write(f"Anne Bilgileri:\n\n")
                f.write(f"╭━━━━━━━━━━━━━━\n")
                f.write(f"┃➥ AD: {annesi['AD']}\n")
                f.write(f"┃➥ SOYAD: {annesi['SOYAD']}\n")
                f.write(f"┃➥ DOĞUM TARİHİ: {annesi['DOGUMTARIHI']}\n")
                f.write(f"┃➥ GSM: {annesi['GSM']}\n")
                f.write(f"╰━━━━━━━━━━━━━━\n\n")

              
                babası = data['babası'][0]
                f.write(f"Baba Bilgileri:\n\n")
                f.write(f"╭━━━━━━━━━━━━━━\n")
                f.write(f"┃➥ AD: {babası['AD']}\n")
                f.write(f"┃➥ SOYAD: {babası['SOYAD']}\n")
                f.write(f"┃➥ DOĞUM TARİHİ: {babası['DOGUMTARIHI']}\n")
                f.write(f"┃➥ GSM: {babası['GSM']}\n")
                f.write(f"╰━━━━━━━━━━━━━━\n\n")

               
                kardesler = data['kardesler']
                f.write(f"Kardeş Bilgileri:\n\n")
                for kardeş in kardesler:
                    f.write(f"╭━━━━━━━━━━━━━━\n")
                    f.write(f"┃➥ AD: {kardeş['AD']}\n")
                    f.write(f"┃➥ SOYAD: {kardeş['SOYAD']}\n")
                    f.write(f"┃➥ DOĞUM TARİHİ: {kardeş['DOGUMTARIHI']}\n")
                    f.write(f"┃➥ GSM: {kardeş['GSM']}\n")
                    f.write(f"╰━━━━━━━━━━━━━━\n\n")

                
                anne_tarafi_kuzenler = data['anne_tarafi_kuzenler']
                f.write(f"Anne Tarafı Kuzenler Bilgileri:\n\n")
                for kuzen in anne_tarafi_kuzenler:
                    f.write(f"╭━━━━━━━━━━━━━━\n")
                    f.write(f"┃➥ AD: {kuzen['AD']}\n")
                    f.write(f"┃➥ SOYAD: {kuzen['SOYAD']}\n")
                    f.write(f"┃➥ DOĞUM TARİHİ: {kuzen['DOGUMTARIHI']}\n")
                    f.write(f"┃➥ GSM: {kuzen['GSM']}\n")
                    f.write(f"╰━━━━━━━━━━━━━━\n\n")

                
                baba_tarafi_kardesler = data['baba_tarafi_kardesler']
                f.write(f"Baba Tarafı Kardeşler Bilgileri:\n\n")
                for kardes in baba_tarafi_kardesler:
                    f.write(f"╭━━━━━━━━━━━━━━\n")
                    f.write(f"┃➥ AD: {kardes['AD']}\n")
                    f.write(f"┃➥ SOYAD: {kardes['SOYAD']}\n")
                    f.write(f"┃➥ DOĞUM TARİHİ: {kardes['DOGUMTARIHI']}\n")
                    f.write(f"┃➥ GSM: {kardes['GSM']}\n")
                    f.write(f"╰━━━━━━━━━━━━━━\n\n")

                
                baba_tarafi_kuzenler = data['baba_tarafi_kuzenler']
                f.write(f"Baba Tarafı Kuzenler Bilgileri:\n\n")
                for kuzen in baba_tarafi_kuzenler:
                    f.write(f"╭━━━━━━━━━━━━━━\n")
                    f.write(f"┃➥ AD: {kuzen['AD']}\n")
                    f.write(f"┃➥ SOYAD: {kuzen['SOYAD']}\n")
                    f.write(f"┃➥ DOĞUM TARİHİ: {kuzen['DOGUMTARIHI']}\n")
                    f.write(f"┃➥ GSM: {kuzen['GSM']}\n")
                    f.write(f"╰━━━━━━━━━━━━━━\n\n")

      
            with open(filename, 'rb') as file:
                bot.send_document(msg.chat.id, file)

        else:
            bot.send_message(msg.chat.id, "Veri alınamadı. Lütfen geçerli bir TC numarası girin.")
    else:
        bot.send_message(msg.chat.id, '*⚠️ Lütfen geçerli bir T.C. Kimlik Numarası girin!.\nÖrnek:* `/sulale 11111111110`')
                       
@bot.message_handler(commands=['slalgsm'])
def slalgsm(msg):
    try:
        user_id = msg.from_user.id
        tc_number = msg.text.split()[1] if len(msg.text.split()) > 1 else None

        if tc_number:
            url = f"https://api.tsgmods.com.tr/t5a12/sulale.php?auth=tsgxyunus&tc={tc_number}"
            response = requests.get(url)
            data = response.json()

            if data['success'] == 'true':
                filename = f"sulalegsm_{tc_number}.txt"
                with open(filename, 'w', encoding='utf-8') as f:
                    kendisi = data['kendisi'][0]
                    f.write(f"Kategori: Kendisi\n")
                    f.write(f"TC: {tc_number}\n")
                    f.write(f"GSM: {kendisi['GSM']}\n")
                    f.write(f"╰━━━━━━━━━━━━━━\n\n")

                    annesi = data['annesi'][0]
                    f.write(f"Kategori: Anne\n")
                    f.write(f"TC: {tc_number}\n")
                    f.write(f"GSM: {annesi['GSM']}\n")
                    f.write(f"╰━━━━━━━━━━━━━━\n\n")

                    babası = data['babası'][0]
                    f.write(f"Kategori: Baba\n")
                    f.write(f"TC: {tc_number}\n")
                    f.write(f"GSM: {babası['GSM']}\n")
                    f.write(f"╰━━━━━━━━━━━━━━\n\n")

                    kardesler = data['kardesler']
                    for kardeş in kardesler:
                        f.write(f"Kategori: Kardeş\n")
                        f.write(f"TC: {tc_number}\n")
                        f.write(f"GSM: {kardeş['GSM']}\n")
                        f.write(f"╰━━━━━━━━━━━━━━\n\n")

                    anne_tarafi_kuzenler = data['anne_tarafi_kuzenler']
                    for kuzen in anne_tarafi_kuzenler:
                        f.write(f"Kategori: Anne Tarafı Kuzeni\n")
                        f.write(f"TC: {tc_number}\n")
                        f.write(f"GSM: {kuzen['GSM']}\n")
                        f.write(f"╰━━━━━━━━━━━━━━\n\n")

                    baba_tarafi_kardesler = data['baba_tarafi_kardesler']
                    for kardes in baba_tarafi_kardesler:
                        f.write(f"Kategori: Baba Tarafı Kardeş\n")
                        f.write(f"TC: {tc_number}\n")
                        f.write(f"GSM: {kardes['GSM']}\n")
                        f.write(f"╰━━━━━━━━━━━━━━\n\n")

                    baba_tarafi_kuzenler = data['baba_tarafi_kuzenler']
                    for kuzen in baba_tarafi_kuzenler:
                        f.write(f"Kategori: Baba Tarafı Kuzeni\n")
                        f.write(f"TC: {tc_number}\n")
                        f.write(f"GSM: {kuzen['GSM']}\n")
                        f.write(f"╰━━━━━━━━━━━━━━\n\n")

                with open(filename, 'rb') as file:
                    bot.send_document(msg.chat.id, file)

                os.remove(filename)
            else:
                bot.send_message(msg.chat.id, "Veri alınamadı. Lütfen geçerli bir TC numarası girin.")
        else:
            bot.send_message(msg.chat.id, "*⚠️ Lütfen geçerli bir T.C. Kimlik Numarası girin!.\nÖrnek:* /slalgsm 11111111110")
    except Exception as e:
        bot.send_message(msg.chat.id, f"Bir hata oluştu: {str(e)}")  
        
                                                                            


@bot.message_handler(commands=["sicil"])
def sicil_command(message):
    try:
        chat_id = message.chat.id

        if not is_private_chat(message):
            bot.send_message(chat_id, "Botu Özelde Kullan.")
            return

        if len(message.text.split()) == 1:
            bot.send_message(chat_id, "╔═══════════════╗\n║ Komutu Doğru Girin!\n║ \n║ Örnek: \n║ /sicil 11111111110 \n╚═══════════════╝")
            return

        tc = message.text.split()[1]
        api_url = f"http://panzehircheck.duckdns.org/api/jessyapi/apicigim/sicil.php?tc={tc}"
        response = requests.get(api_url).json()

        if not response:
            bot.send_message(chat_id, "Sicil bilgisi bulunamadı.")
            return
        
        result = response[0]
        output = f"""
╔═══════════════
╟ @DarkVipRoom
╚═══════════════

╔═══════════════
╟ TC: {result["KIMLIKNO"]}
╟ ADI: {result["ISIM"]}
╟ SOY ADI: {result["SOYISIM"]}
╟ SAYI: {result["SAYI"]}
╟ S. TÜRÜ: {result["SORGUTURU"]}
╟ K. TÜRÜ: {result["KIMLIKTURU"]}
╟ SİCİL: {result["SICILKAYIT"]}  # Corrected the field name here
╟ İŞLENEN YER: {result["SICILINISLENDIGIYER"]}
╚═══════════════
"""
        bot.send_message(chat_id, output)
    except IndexError:
        bot.send_message(chat_id, "╔═══════════════╗\n║ Komutu Doğru Girin!\n║ \n║ Örnek: \n║ /sicil 11111111110 \n╚═══════════════╝")
    except Exception as e:
        bot.send_message(chat_id, f"HATA: {str(e)}")


def is_private_chat(message):
    return message.chat.type == "private"                                 
                                
# Botu başlat
bot.polling()
    
