# Sentiment Platform — Müşteri Paneli

## Ne yapar
Müşterilerin kendi dashboard'u. Statik HTML, GitHub Pages. Supabase anon key + RLS ile çalışır.

**URL:** https://selimkayacan.github.io/sentiment-customer-panel

## Sekmeler
| Tab | id | Açıklama |
|-----|----|----------|
| Genel Bakış | `tab-overview` | Sentiment skoru, trend grafiği, kategori durumu, en iyi/kötü yorumlar |
| Şubeler | `tab-locations` | Şube karşılaştırması, şube detayı trendi, karşılaştırma grafiği |
| Sosyal Medya | `tab-social` | Twitter + Ekşi sentiment |
| Kategoriler | `tab-categories` | Kategori bazında bar chart |
| Alertler | `tab-alerts` | SaaS Full only |
| Aksiyonlar | `tab-actions` | SaaS Full only |
| Raporlar | `tab-reports` | Manager only |

## Önemli state değişkenleri
```js
let currentCustomer, currentBrandId;
let allLocations = [];
let trendChart, catChart, locTrendChart, compareChart;
let trendMode = 'score'; // 'score' | 'volume'
let currentOpenLocId = null;        // açık şubenin ID'si
let currentOpenLocReviews = [];     // açık şubenin ham yorumları
let compareRawData = {};            // {locId: {name, reviews}} — karşılaştırma ham verisi
```

## Genel Bakış filtreler
```html
<select id="trend-cat-filter">   <!-- Tüm Kategoriler / kategori adları -->
<select id="trend-period">       <!-- Son 12/24/6 Ay / Tümü -->
<button id="btn-trend-score">    <!-- Skor modu -->
<button id="btn-trend-vol">      <!-- Hacim modu -->
```
Değişince `reloadTrend()` tetiklenir.

## Şubeler sayfası filtreler
```html
<select id="loc-cat-filter">  <!-- şubeye özgü kategoriler, şube açılınca dolar -->
<select id="loc-period">      <!-- Son 12/24/6 Ay / Tümü -->
```
Değişince `reloadLocTrend()` tetiklenir → hem `loc-trend-chart` hem `loc-compare-chart` yeniden çizilir.

## Temel fonksiyonlar
| Fonksiyon | Açıklama |
|-----------|----------|
| `loadOverview()` | Genel bakış verilerini yükler |
| `loadTrend(locIds)` | Trend grafiğini çizer, filtreler uygular |
| `reloadTrend()` | Filtre değişince trend yeniler |
| `openLoc(locId, name, lat, lng)` | Şube detayını açar, currentOpenLocId/Reviews set eder, loc-cat-filter sıfırlar |
| `drawLocTrend(locId, reviews)` | Şube trend grafiği, loc-period + loc-cat-filter uygular |
| `populateLocCategoryFilter(revIds)` | Şube kategorilerini dropdown'a doldurur (bir kez) |
| `reloadLocTrend()` | Şube filtresi değişince çağrılır |
| `addToCompare()` | compareRawData'ya ham reviews ekler |
| `renderCompareChart()` | async, filtreli karşılaştırma grafiği çizer |
| `clearCompare()` | compareRawData + chart temizler |

## Supabase yardımcısı
```js
sbChunkedIn(table, select, column, ids, filters)
// ids > 50 ise chunk chunk sorgu atar
```

## Son değişiklikler
- Şubeler sayfasına tarih (Son 6/12/24 Ay/Tümü) ve kategori filtresi eklendi
- Filtre değişince loc-trend-chart + loc-compare-chart yeniden çiziliyor
- compareData → compareRawData (ham reviews saklar, async render)
- Kategori seçilince grafik rengi mora dönüyor (genel bakıştaki gibi)
