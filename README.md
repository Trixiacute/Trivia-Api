# Trivia API Bahasa Indonesia

API Trivia dalam Bahasa Indonesia yang dapat diintegrasikan dengan bot Discord atau aplikasi lainnya. API ini menyediakan pertanyaan-pertanyaan trivia dalam berbagai kategori dan tingkat kesulitan.

## 📋 Fitur

- ✅ Pertanyaan trivia dalam bahasa Indonesia
- ✅ Berbagai kategori seperti Geografi, Sejarah, Sains, dan lainnya
- ✅ Filter pertanyaan berdasarkan kategori
- ✅ Berbagai tingkat kesulitan (Mudah, Sedang, Sulit)
- ✅ Endpoint API REST yang mudah digunakan
- ✅ Dokumentasi interaktif dan lengkap
- ✅ Demo aplikasi web untuk menguji API
- ✅ Siap diintegrasikan dengan Discord Bot
- ✅ Status Page untuk memonitor kondisi layanan
- ✅ Health Metrics untuk memonitor performa sistem
- ✅ Endpoint khusus untuk Discord Bot dengan format embed

## 📖 Dokumentasi API

### Dokumentasi Interaktif

API ini dilengkapi dengan dokumentasi interaktif yang lengkap.

- Akses dokumentasi API di `/docs`
- Anda dapat melihat semua endpoint tersedia dan contoh penggunaannya
- Tersedia contoh kode dalam berbagai bahasa pemrograman

### Demo Aplikasi

Tersedia aplikasi demo untuk menguji API secara interaktif:

- Akses demo di root path `/` 
- Lihat statistik jawaban benar dan salah
- Pilih pertanyaan berdasarkan kategori

### Status Page

Halaman status tersedia untuk memonitor kondisi layanan:

- Akses status page di `/status`
- Menyediakan informasi up-to-date tentang kondisi layanan
- Menampilkan incident history dan SLA

## 📝 Endpoint API

### Endpoint Trivia

```
GET /api/trivia/random           # Mendapatkan pertanyaan acak
GET /api/trivia/category/:kategori  # Mendapatkan pertanyaan berdasarkan kategori
GET /api/trivia/categories       # Mendapatkan semua kategori
GET /api/trivia/discord          # Mendapatkan pertanyaan format Discord (embeds)
```

### Endpoint Status dan Health Metrics

```
GET /api/status                 # Status seluruh layanan
GET /api/status/history         # Riwayat insiden
GET /api/status/sla             # Informasi Service Level Agreement
GET /api/status/ping            # Health check sederhana (untuk monitoring)

GET /api/system/status          # Informasi dasar status sistem (uptime, versi)
GET /api/system/health          # Informasi detail metrik sistem (memori, CPU, layanan)
```

## 📊 Struktur Respons

### Respon Trivia

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

### Respons System Health

```json
{
  "status": "success",
  "server": {
    "platform": "linux",
    "nodeVersion": "v16.x.x",
    "uptime": "2d 5h 30m 15s",
    "timestamp": "2023-06-01T12:00:00Z",
    "memoryUsage": "156.5 MB",
    "freeMemory": "8.2 GB",
    "totalMemory": "16 GB"
  },
  "services": {
    "api": { "status": "healthy", "message": "API is responding" },
    "database": { "status": "healthy", "message": "Database connection is stable" },
    "cache": { "status": "healthy", "message": "Cache service is operating normally" }
  },
  "environment": "production"
}
```

### Respons Discord Trivia

```json
{
  "status": "success",
  "data": {
    "original": {
      "id": 1,
      "pertanyaan": "Apa ibukota Indonesia?",
      "jawaban": "Jakarta",
      "pilihan": ["Surabaya", "Bandung", "Jakarta", "Medan"],
      "kategori": "Geografi",
      "tingkat_kesulitan": "Mudah"
    },
    "discord": {
      "embed": {
        "title": "📝 Pertanyaan Trivia",
        "description": "Apa ibukota Indonesia?",
        "color": 2664261,
        "fields": [
          {
            "name": "Pilihan",
            "value": "🇦 Surabaya\n🇧 Bandung\n🇨 Jakarta\n🇩 Medan"
          },
          {
            "name": "Kategori",
            "value": "Geografi",
            "inline": true
          },
          {
            "name": "Tingkat Kesulitan",
            "value": "Mudah",
            "inline": true
          }
        ],
        "footer": {
          "text": "Trivia API Bahasa Indonesia | Gunakan reaksi untuk menjawab"
        }
      },
      "answer": "Jakarta",
      "answer_index": 2,
      "answer_emoji": "🇨",
      "options_emojis": ["🇦", "🇧", "🇨", "🇩"]
    }
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
      const response = await axios.get('https://trivia-api-sooty.vercel.app/api/trivia/random');
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

### Menggunakan Endpoint Discord:

```javascript
const { Client, GatewayIntentBits, EmbedBuilder } = require('discord.js');
const axios = require('axios');

const client = new Client({ 
  intents: [
    GatewayIntentBits.Guilds,
    GatewayIntentBits.GuildMessages,
    GatewayIntentBits.MessageContent,
    GatewayIntentBits.GuildMessageReactions
  ] 
});

client.on('ready', () => {
  console.log(`Bot telah login sebagai ${client.user.tag}!`);
});

client.on('messageCreate', async message => {
  if (message.content === '!trivia') {
    try {
      // Menggunakan endpoint khusus Discord
      const response = await axios.get('https://your-vercel-deployment.vercel.app/api/trivia/discord');
      const { discord, original } = response.data.data;
      
      // Membuat embed dari data yang disediakan API
      const embed = new EmbedBuilder()
        .setTitle(discord.embed.title)
        .setDescription(discord.embed.description)
        .setColor(discord.embed.color);
      
      // Menambahkan field
      discord.embed.fields.forEach(field => {
        embed.addFields({ name: field.name, value: field.value, inline: field.inline });
      });
      
      // Menambahkan footer
      if (discord.embed.footer) {
        embed.setFooter({ text: discord.embed.footer.text });
      }
      
      // Mengirim embed dan menunggu emoji
      const sentMessage = await message.channel.send({ embeds: [embed] });
      
      // Menambahkan reaksi untuk setiap pilihan
      for (const emoji of discord.options_emojis) {
        await sentMessage.react(emoji);
      }
      
      // Filter: hanya user yang sama dan emoji yang valid
      const filter = (reaction, user) => {
        return discord.options_emojis.includes(reaction.emoji.name) && user.id === message.author.id;
      };
      
      // Menunggu reaksi
      sentMessage.awaitReactions({ filter, max: 1, time: 30000, errors: ['time'] })
        .then(collected => {
          const reaction = collected.first();
          
          if (reaction.emoji.name === discord.answer_emoji) {
            message.channel.send('✅ Benar! Jawaban yang tepat.');
          } else {
            message.channel.send(`❌ Salah! Jawaban yang benar adalah: ${original.jawaban}`);
          }
        })
        .catch(collected => {
          message.channel.send(`⏱️ Waktu habis! Jawaban yang benar adalah: ${original.jawaban}`);
        });
      
    } catch (error) {
      console.error('Error mengambil pertanyaan trivia:', error);
      message.channel.send('Maaf, terjadi kesalahan saat mengambil pertanyaan trivia.');
    }
  }
});

client.login('TOKEN_BOT_DISCORD_ANDA');
```

## 🔍 Monitoring dan Status

### Status Page

API ini dilengkapi dengan Status Page untuk memonitor kondisi layanan secara real-time:

- Akses Status Page di `/status`
- Menampilkan kondisi semua komponen sistem
- Mencatat dan menampilkan insiden yang terjadi
- Menyediakan informasi SLA (Service Level Agreement)

### Health Metrics

Endpoint health metrics menyediakan informasi detail tentang kondisi sistem:

- `GET /api/system/health` - Informasi lengkap tentang kondisi sistem, termasuk:
  - Penggunaan memori
  - Uptime server
  - Status layanan internal
  - Versi dan lingkungan sistem

- `GET /api/system/status` - Informasi dasar uptime dan versi

### Rate Limiting

API ini mengimplementasikan rate limiting untuk menjaga stabilitas layanan:

- Endpoint trivia: 60 permintaan per menit
- Endpoint system: 10 permintaan per menit
- Endpoint status/ping: 300 permintaan per menit
- Endpoint status lainnya: 60 permintaan per menit
  
Proyek ini dilisensikan di bawah lisensi MIT - lihat file LICENSE untuk detail lebih lanjut. 
