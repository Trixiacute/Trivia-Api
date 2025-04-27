# Trivia API Bahasa Indonesia

API Trivia dalam Bahasa Indonesia yang dapat diintegrasikan dengan bot Discord atau aplikasi lainnya. API ini menyediakan pertanyaan-pertanyaan trivia dalam berbagai kategori dan tingkat kesulitan.

## 📋 Fitur

- ✅ Pertanyaan trivia dalam bahasa Indonesia
- ✅ Berbagai kategori seperti Geografi, Sejarah, Sains, dan lainnya
- ✅ Filter pertanyaan berdasarkan kategori
- ✅ Berbagai tingkat kesulitan (Mudah, Sedang, Sulit)
- ✅ Endpoint API REST yang mudah digunakan
- ✅ Dokumentasi Swagger/OpenAPI interaktif
- ✅ Demo aplikasi web untuk menguji API
- ✅ Siap diintegrasikan dengan Discord Bot

### Dokumentasi Swagger/OpenAPI

API ini dilengkapi dengan dokumentasi interaktif menggunakan Swagger UI.

- Akses dokumentasi API di `/api-docs`
- Anda dapat menguji endpoint langsung dari UI Swagger

### Demo Aplikasi

Tersedia aplikasi demo untuk menguji API secara interaktif:

- Akses demo di root path `/` 
- Lihat statistik jawaban benar dan salah
- Pilih pertanyaan berdasarkan kategori

## 📝 Endpoint API

### Mendapatkan pertanyaan acak

```
GET /api/trivia/random
```

### Mendapatkan pertanyaan berdasarkan kategori

```
GET /api/trivia/category/:kategori
```

Contoh: `/api/trivia/category/sejarah`

### Mendapatkan semua kategori

```
GET /api/trivia/categories
```

## 📊 Struktur Respons

```json
{
  "status": "success",
  "data": {
    "id": 1,
    "pertanyaan": "Apa ibukota Indonesia?",
    "jawaban": "Jakarta",
    "pilihan": ["Surabaya", "Bandung", "Jakarta", "Medan"],
    "kategori": "Geografi",
    "tingkat_kesulitan": "Mudah"
  }
}
```

## 🤖 Integrasi dengan Discord Bot

Untuk mengintegrasikan API ini dengan bot Discord Anda, gunakan library Discord.js dan axios.

### Contoh kode dasar:

```javascript
const { Client, GatewayIntentBits } = require('discord.js');
const axios = require('axios');

const client = new Client({ 
  intents: [
    GatewayIntentBits.Guilds,
    GatewayIntentBits.GuildMessages,
    GatewayIntentBits.MessageContent
  ] 
});

client.on('ready', () => {
  console.log(`Bot telah login sebagai ${client.user.tag}!`);
});

client.on('messageCreate', async message => {
  if (message.content === '!trivia') {
    try {
      const response = await axios.get('https://your-vercel-deployment.vercel.app/api/trivia/random');
      const question = response.data.data;
      
      let triviaMessage = `**Pertanyaan:** ${question.pertanyaan}\n\n`;
      triviaMessage += `**Pilihan:**\n`;
      question.pilihan.forEach((pilihan, index) => {
        triviaMessage += `${['A', 'B', 'C', 'D'][index]}. ${pilihan}\n`;
      });
      
      message.channel.send(triviaMessage);
      
      // Kode untuk memeriksa jawaban dapat ditambahkan di sini
    } catch (error) {
      console.error('Error mengambil pertanyaan trivia:', error);
      message.channel.send('Maaf, terjadi kesalahan saat mengambil pertanyaan trivia.');
    }
  }
});

client.login('TOKEN_BOT_DISCORD_ANDA');
```

### Memeriksa jawaban dari pengguna:

```javascript
// Tambahkan ini setelah mengirim pertanyaan
const filter = m => m.author.id === message.author.id;
const collector = message.channel.createMessageCollector({ filter, time: 30000 });

collector.on('collect', m => {
  const jawaban = m.content.toUpperCase();
  const jawabanIndex = ['A', 'B', 'C', 'D'].indexOf(jawaban);
  
  if(jawabanIndex !== -1) {
    if(question.pilihan[jawabanIndex] === question.jawaban) {
      message.channel.send('✅ Benar! Jawaban yang tepat.');
    } else {
      message.channel.send(`❌ Salah! Jawaban yang benar adalah: ${question.jawaban}`);
    }
    collector.stop();
  }
});

collector.on('end', (collected, reason) => {
  if(reason === 'time') {
    message.channel.send(`⏱️ Waktu habis! Jawaban yang benar adalah: ${question.jawaban}`);
  }
});
```

## 🛠️ Pengembangan

### Menambahkan Pertanyaan Baru

Anda dapat menambahkan lebih banyak pertanyaan dengan mengedit file `src/data/trivia.json`. Lihat [CONTRIBUTING.md](CONTRIBUTING.md) untuk petunjuk format.

### Struktur Proyek

```
├── public/               # File statis (frontend demo)
│   ├── index.html        # Halaman demo HTML
│   ├── styles.css        # Stylesheet demo
│   └── script.js         # JavaScript demo
├── src/
│   ├── config/           # Konfigurasi aplikasi
│   │   └── swagger.js    # Konfigurasi Swagger
│   ├── controllers/      # Logic bisnis
│   ├── data/             # Data JSON
│   ├── models/           # Model data
│   ├── routes/           # Definisi rute API
│   └── index.js          # Entry point
├── .env                  # Konfigurasi lingkungan
├── package.json          # Dependencies
└── vercel.json           # Konfigurasi Vercel
```

## 👥 Kontribusi

Kontribusi sangat diterima! Lihat [CONTRIBUTING.md](CONTRIBUTING.md) untuk petunjuk.

## 📄 Lisensi

Proyek ini dilisensikan di bawah lisensi MIT - lihat file LICENSE untuk detail lebih lanjut. 
