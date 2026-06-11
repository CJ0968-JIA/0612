<script setup>
import { ref, computed, watch, onMounted } from 'vue'

const needsOptions = [
  { label: '提神', value: '提神' },
  { label: '解膩', value: '解膩' },
  { label: '想咬東西', value: '想咬東西' },
  { label: '補充能量', value: '補充能量' },
  { label: '清爽提味', value: '清爽提味' },
  { label: '舒緩放鬆', value: '舒緩放鬆' },
  { label: '消暑解渴', value: '消暑解渴' },
  { label: '增進食慾', value: '增進食慾' },
  { label: '甜蜜享受', value: '甜蜜享受' },
  { label: '健康輕盈', value: '健康輕盈' },
]

const flavorOptions = [
  { label: '純茶', value: '純茶' },
  { label: '鮮奶拿鐵', value: '鮮奶拿鐵' },
  { label: '果茶', value: '果茶' },
  { label: '氣泡飲', value: '氣泡飲' },
  { label: '奶蓋', value: '奶蓋' },
  { label: '咖啡飲', value: '咖啡飲' },
  { label: '巧克力', value: '巧克力' },
  { label: '烏龍茶', value: '烏龍茶' },
  { label: '抹茶', value: '抹茶' },
  { label: '芋頭', value: '芋頭' },
]

const addOnLevels = [
  { value: 0, label: '無加料' },
  { value: 1, label: '輕量加料' },
  { value: 2, label: '標準加料' },
  { value: 3, label: '滿配加料' },
]

const sweetnessLabels = ['無糖', '微糖', '少糖', '正常', '多糖']
const sweetnessValues = [0, 12, 25, 40, 58]
const addOnSets = [[], ['珍珠'], ['珍珠', '仙草凍'], ['珍珠', '仙草凍', '布丁']]
const addOnCalories = { 珍珠: 80, 仙草凍: 35, 布丁: 70 }

const selectedNeeds = ref('')
const selectedFlavors = ref('')
const sweetness = ref(2)
const addOnLevel = ref(1)
const userCoords = ref(null)
const mapError = ref('')
const message = ref('請先使用多選選擇你的當下需求與偏好，系統將幫你尋找最合適的飲料。')
const mapIframeSrc = ref('')

const drinks = [
  { id: 1, brand: '五十嵐', name: '紅茶拿鐵', tags: ['提神', '鮮奶拿鐵'], baseCalories: 150 },
  { id: 2, brand: '茶湯會', name: '四季春青茶', tags: ['提神', '純茶'], baseCalories: 5 },
  { id: 3, brand: 'COCO', name: '芒果綠茶', tags: ['解膩', '果茶'], baseCalories: 80 },
  { id: 4, brand: '清心福全', name: '熟成紅茶', tags: ['解膩', '純茶'], baseCalories: 0 },
  { id: 5, brand: '50嵐', name: '珍珠鮮奶茶', tags: ['想咬東西', '鮮奶拿鐵'], baseCalories: 170 },
  { id: 6, brand: '迷客夏', name: '百香果綠茶', tags: ['解膩', '果茶'], baseCalories: 65 },
  { id: 7, brand: '樂檸', name: '氣泡檸檬', tags: ['提神', '氣泡飲'], baseCalories: 10 },
  { id: 8, brand: '珍煮丹', name: '黑糖鮮奶', tags: ['想咬東西', '鮮奶拿鐵'], baseCalories: 210 },
  { id: 9, brand: '51 Tea', name: '烏龍奶茶', tags: ['補充能量', '鮮奶拿鐵'], baseCalories: 160 },
  { id: 10, brand: '丹堤', name: '焙茶拿鐵', tags: ['舒緩放鬆', '鮮奶拿鐵'], baseCalories: 140 },
  { id: 11, brand: 'Tiger Sugar', name: '黑糖珍奶', tags: ['甜蜜享受', '奶蓋'], baseCalories: 220 },
  { id: 12, brand: '快樂檸檬', name: '檸檬綠茶', tags: ['消暑解渴', '果茶'], baseCalories: 45 },
  { id: 13, brand: '春水堂', name: '珍珠奶綠', tags: ['想咬東西', '奶蓋'], baseCalories: 185 },
  { id: 14, brand: 'Comebuy', name: '四季春青茶', tags: ['清爽提味', '純茶'], baseCalories: 8 },
  { id: 15, brand: '知客茶館', name: '冷泡茶', tags: ['健康輕盈', '純茶'], baseCalories: 0 },
  { id: 16, brand: '黑沃咖啡', name: '黑咖啡', tags: ['提神', '咖啡飲'], baseCalories: 20 },
  { id: 17, brand: '咖啡陪你', name: '卡布奇諾', tags: ['提神', '咖啡飲'], baseCalories: 130 },
  { id: 18, brand: '85度C', name: '冷頷朵', tags: ['補充能量', '咖啡飲'], baseCalories: 110 },
  { id: 19, brand: '滿杯', name: '巧克力奶茶', tags: ['甜蜜享受', '巧克力'], baseCalories: 190 },
  { id: 20, brand: '貴族派', name: '伯爵奶茶', tags: ['舒緩放鬆', '烏龍茶'], baseCalories: 155 },
  { id: 21, brand: '鹿點', name: '抹茶拿鐵', tags: ['補充能量', '抹茶'], baseCalories: 135 },
  { id: 22, brand: '甘茶月光', name: '芋頭牛奶', tags: ['甜蜜享受', '芋頭'], baseCalories: 200 },
  { id: 23, brand: '奶茶先生', name: '檸檬氣泡水', tags: ['消暑解渴', '氣泡飲'], baseCalories: 12 },
  { id: 24, brand: '轉角茶館', name: '桂花烏龍', tags: ['清爽提味', '烏龍茶'], baseCalories: 18 },
  { id: 25, brand: '糖村', name: '紅豆抹茶', tags: ['想咬東西', '抹茶'], baseCalories: 175 },
  { id: 26, brand: '英記茶莊', name: '凍頂烏龍茶', tags: ['增進食慾', '烏龍茶'], baseCalories: 10 },
  { id: 27, brand: 'i-Cream', name: '冰淇淋奶茶', tags: ['甜蜜享受', '奶蓋'], baseCalories: 240 },
  { id: 28, brand: '老芋仁', name: '芋頭綠茶', tags: ['想咬東西', '芋頭'], baseCalories: 165 },
]

const stores = [
  { id: 1, name: '市府門市', coords: { lat: 25.0478, lng: 121.5319 }, menu: ['紅茶拿鐵', '珍珠鮮奶茶', '熟成紅茶', '烏龍奶茶', '黑糖珍奶'] },
  { id: 2, name: '信義門市', coords: { lat: 25.0340, lng: 121.5623 }, menu: ['四季春青茶', '芒果綠茶', '百香果綠茶', '檸檬綠茶', '珍珠奶綠'] },
  { id: 3, name: '松山門市', coords: { lat: 25.0535, lng: 121.5778 }, menu: ['氣泡檸檬', '黑糖鮮奶', '百香果綠茶', '檸檬氣泡水', '焙茶拿鐵'] },
  { id: 4, name: '大安門市', coords: { lat: 25.0320, lng: 121.5437 }, menu: ['紅茶拿鐵', '四季春青茶', '氣泡檸檬', '卡布奇諾', '冷頷朵'] },
  { id: 5, name: '中山門市', coords: { lat: 25.0520, lng: 121.5220 }, menu: ['冷泡茶', '黑咖啡', '珍珠奶綠', '伯爵奶茶', '巧克力奶茶'] },
  { id: 6, name: '忠孝門市', coords: { lat: 25.0405, lng: 121.5530 }, menu: ['抹茶拿鐵', '芋頭牛奶', '烏龍奶茶', '紅豆抹茶', '芋頭綠茶'] },
  { id: 7, name: '南京門市', coords: { lat: 25.0555, lng: 121.5415 }, menu: ['焙茶拿鐵', '桂花烏龍', '凍頂烏龍茶', '冰淇淋奶茶', '檸檬綠茶'] },
  { id: 8, name: '東區門市', coords: { lat: 25.0365, lng: 121.5680 }, menu: ['黑糖珍奶', '珍珠鮮奶茶', '卡布奇諾', '抹茶拿鐵', '黑咖啡'] },
  { id: 9, name: '西門門市', coords: { lat: 25.0417, lng: 121.5088 }, menu: ['四季春青茶', '珍珠奶綠', '巧克力奶茶', '檸檬氣泡水', '伯爵奶茶'] },
  { id: 10, name: '天母門市', coords: { lat: 25.0690, lng: 121.5520 }, menu: ['冷泡茶', '芋頭牛奶', '焙茶拿鐵', '紅豆抹茶', '凍頂烏龍茶'] },
  { id: 11, name: '內湖門市', coords: { lat: 25.0833, lng: 121.5900 }, menu: ['抹茶拿鐵', '黑咖啡', '四季春青茶', '百香果綠茶', '紅茶拿鐵'] },
  { id: 12, name: '板橋門市', coords: { lat: 25.0125, lng: 121.4658 }, menu: ['珍珠鮮奶茶', '芒果綠茶', '熟成紅茶', '黑糖鮮奶', '冷泡茶'] },
  { id: 13, name: '南港門市', coords: { lat: 25.0526, lng: 121.6062 }, menu: ['烏龍奶茶', '焙茶拿鐵', '氣泡檸檬', '珍珠奶綠', '檸檬綠茶'] },
  { id: 14, name: '士林門市', coords: { lat: 25.0931, lng: 121.5262 }, menu: ['伯爵奶茶', '冷頷朵', '黑糖珍奶', '冰淇淋奶茶', '卡布奇諾'] },
  { id: 15, name: '公館門市', coords: { lat: 25.0135, lng: 121.5365 }, menu: ['珍珠鮮奶茶', '四季春青茶', '百香果綠茶', '黑咖啡', '巧克力奶茶'] },
  { id: 16, name: '永和門市', coords: { lat: 25.0080, lng: 121.5140 }, menu: ['紅茶拿鐵', '熟成紅茶', '黑糖珍奶', '抹茶拿鐵', '桂花烏龍'] },
  { id: 17, name: '新莊門市', coords: { lat: 25.0359, lng: 121.4451 }, menu: ['四季春青茶', '百香果綠茶', '檸檬氣泡水', '紅豆抹茶', '烏龍奶茶'] },
  { id: 18, name: '三重門市', coords: { lat: 25.0631, lng: 121.4912 }, menu: ['黑咖啡', '氣泡檸檬', '珍珠奶綠', '巧克力奶茶', '凍頂烏龍茶'] },
  { id: 19, name: '中和門市', coords: { lat: 24.9980, lng: 121.4950 }, menu: ['紅茶拿鐵', '芒果綠茶', '焙茶拿鐵', '伯爵奶茶', '芋頭牛奶'] },
  { id: 20, name: '萬華門市', coords: { lat: 25.0354, lng: 121.4997 }, menu: ['冷泡茶', '抹茶拿鐵', '熟成紅茶', '黑糖珍奶', '珍珠鮮奶茶'] },
]

const selectedAddOns = computed(() => addOnSets[addOnLevel.value] || [])

const filteredDrinks = computed(() => {
  // 如果沒有選擇任何提示詞，返回前 3 款飲料
  if (!selectedNeeds.value && !selectedFlavors.value) {
    return drinks.slice(0, 3)
  }

  // 第一層篩選：精確匹配選定的提示詞
  const exactMatches = drinks.filter((drink) => {
    const needsMatch = !selectedNeeds.value || drink.tags.includes(selectedNeeds.value)
    const flavorsMatch = !selectedFlavors.value || drink.tags.includes(selectedFlavors.value)
    return needsMatch && flavorsMatch
  })

  // 如果精確匹配結果 >= 3 款，返回前 3 款
  if (exactMatches.length >= 3) {
    return exactMatches.slice(0, 3)
  }

  // 第二層：如果精確匹配少於 3 款，補充相關飲品
  if (exactMatches.length > 0) {
    const remaining = drinks.filter((drink) => !exactMatches.includes(drink)).slice(0, 3 - exactMatches.length)
    return [...exactMatches, ...remaining]
  }

  // 第三層保底：返回前 3 款飲料
  return drinks.slice(0, 3)
})

const recommendedDrinks = computed(() => filteredDrinks.value)

// 監控篩選結果，更新訊息
watch(
  () => ({
    need: selectedNeeds.value,
    flavor: selectedFlavors.value,
  }),
  ({ need, flavor }) => {
    if (!need && !flavor) {
      message.value = '👉 點選「當下需求」或「偏好口味」中任一提示詞，馬上跳出對應飲品推薦！'
    } else {
      message.value = `已選擇：${need || ''}${need && flavor ? ' + ' : ''}${flavor || ''}，為你精選最符合的三款飲品。`
    }
  },
  { deep: true }
)

const drinkWithCalories = (drink) => {
  if (!drink) return { ...drink, totalCalories: 0, sugarCalories: 0, addOnCalories: 0 }
  const sugarCal = sweetnessValues[sweetness.value] || 0
  const addOnCal = selectedAddOns.value.reduce(
    (total, item) => total + (addOnCalories[item] || 0),
    0
  )
  const totalCalories = drink.baseCalories + sugarCal + addOnCal
  return {
    ...drink,
    sugarCalories: sugarCal,
    addOnCaloriesTotal: addOnCal,
    totalCalories,
  }
}

const currentCalories = (drink) => {
  if (!drink) return 0
  const sugar = sweetnessValues[sweetness.value] || 0
  const addOnCal = selectedAddOns.value.reduce(
    (total, item) => total + (addOnCalories[item] || 0),
    0
  )
  return drink.baseCalories + sugar + addOnCal
}

const recommendedStores = computed(() => {
  const storesWithDrink = []
  for (const drink of recommendedDrinks.value) {
    // 改用 filter 找出所有販售該飲品的門市，而非僅第一間
    const matches = stores.filter((store) => store.menu.includes(drink.name))
    matches.forEach(match => {
      storesWithDrink.push({
        ...match,
        drink: drink.name,
        calories: currentCalories(drink),
        matchedTags: drink.tags, // 添加匹配的標籤
      })
    })
  }

  // 移除重複的門市並回傳更多選項（例如最多 6 筆）
  const uniqueStores = Array.from(new Set(storesWithDrink.map(s => s.id)))
    .map(id => storesWithDrink.find(s => s.id === id));

  return uniqueStores.length > 0 
    ? uniqueStores.slice(0, 6) 
    : stores.slice(0, 6).map((store, index) => ({
        ...store,
        drink: recommendedDrinks.value[index % recommendedDrinks.value.length]?.name || store.menu[0],
        calories: currentCalories(recommendedDrinks.value[index % recommendedDrinks.value.length]) || 0,
        matchedTags: recommendedDrinks.value[index % recommendedDrinks.value.length]?.tags || [],
      }))
})

const distanceKm = (pointA, pointB) => {
  const toRad = (deg) => (deg * Math.PI) / 180
  const R = 6371
  const dLat = toRad(pointB.lat - pointA.lat)
  const dLng = toRad(pointB.lng - pointA.lng)
  const a =
    Math.sin(dLat / 2) ** 2 +
    Math.cos(toRad(pointA.lat)) *
      Math.cos(toRad(pointB.lat)) *
      Math.sin(dLng / 2) ** 2
  const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a))
  return R * c
}

const walkCalories = (km) => Math.round(km * 55)

const walkRatio = (calorie, km) => {
  if (!km || calorie === 0) return 0
  return Math.min(100, Math.round((walkCalories(km) / calorie) * 100))
}

const generateMapIframe = () => {
  if (!userCoords.value) {
    mapError.value = '尚未取得定位，請先啟用 GPS。'
    return
  }
  
  const center = userCoords.value
  const zoom = 15
  
  // 生成嵌入式 iframe URL (使用 Google Maps 嵌入格式)
  const iframeUrl = `https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d3615.2234!2d${center.lng}!3d${center.lat}!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x0%3A0x0!2zMyDCsDAwJzAwLjAiTiAxMjDCsDAwJzAwLjAiRQ!5e0!3m2!1szh-TW!2stw!4v1234567890`
  
  // 簡化版 - 使用基礎 Google Maps URL
  const simplifiedUrl = `https://maps.google.com/maps?q=${center.lat},${center.lng}&z=${zoom}&output=embed`
  mapIframeSrc.value = simplifiedUrl
  mapError.value = ''
}

const cartItems = ref([])

const toggleSelection = (category, value) => {
  if (category === 'need') {
    selectedNeeds.value = selectedNeeds.value === value ? '' : value
  } else if (category === 'flavor') {
    selectedFlavors.value = selectedFlavors.value === value ? '' : value
  }
}

const addToCart = (drink) => {
  const item = {
    id: Date.now(),
    brand: drink.brand,
    name: drink.name,
    sweetness: sweetnessLabels[sweetness.value],
    addOns: selectedAddOns.value,
    totalCalories: currentCalories(drink),
  }
  cartItems.value.push(item)
}

const removeFromCart = (itemId) => {
  cartItems.value = cartItems.value.filter((item) => item.id !== itemId)
}

const copyOrderToClipboard = async () => {
  if (cartItems.value.length === 0) {
    alert('購物車是空的，請先加入飲料！')
    return
  }
  const orderText = cartItems.value
    .map(
      (item) =>
        `${item.brand}-${item.name}(${item.sweetness}${item.addOns.length ? '/' + item.addOns.join('、') : ''}) ${item.totalCalories}大卡`
    )
    .join('\n')
  try {
    await navigator.clipboard.writeText(orderText)
    alert('✅ 訂單已複製到剪貼簿！')
  } catch (err) {
    alert('❌ 複製失敗，請手動複製。')
  }
}

const getCalorieGrade = (calories) => {
  if (calories <= 350) {
    return {
      level: '輕盈級',
      emoji: '🌿',
      sin: '罪行指數：輕度珍奶愛好者',
      exercise: '步行 30 分鐘消耗完畢',
    }
  } else if (calories <= 450) {
    return {
      level: '中等級',
      emoji: '⚠️',
      sin: '罪行指數：資深珍奶迷',
      exercise: '需要深蹲 50 次或打 30 分鐘傳說對決',
    }
  } else {
    return {
      level: '重度級',
      emoji: '🚨',
      sin: '罪行指數：重度珍奶成癮罪！',
      exercise: '需要深蹲 2 小時或瘋狂打傳說對決來消耗',
    }
  }
}

const requestLocation = () => {
  if (!navigator.geolocation) {
    mapError.value = '瀏覽器不支援 GPS 定位功能。'
    return
  }
  mapError.value = '正在取得定位...'
  navigator.geolocation.getCurrentPosition(
    (position) => {
      userCoords.value = {
        lat: position.coords.latitude,
        lng: position.coords.longitude,
      }
      mapError.value = ''
      generateMapIframe()
    },
    (error) => {
      console.error('Geolocation error:', error)
      if (error.code === 1) {
        mapError.value = '定位被拒絕。請在瀏覽器設定中允許定位權限。'
      } else if (error.code === 2) {
        mapError.value = '無法取得定位。請確認 GPS 已啟用且有訊號。'
      } else if (error.code === 3) {
        mapError.value = '定位請求逾時，請重試。'
      } else {
        mapError.value = '無法取得定位，請允許定位權限後重新點擊按鈕。'
      }
    },
    {
      enableHighAccuracy: true,
      timeout: 10000,
      maximumAge: 0,
    }
  )
}

const useTestCoordinates = () => {
  // 使用台北市政府位置作為測試座標
  userCoords.value = {
    lat: 25.0478,
    lng: 121.5319,
  }
  mapError.value = ''
  generateMapIframe()
}

onMounted(() => {
  setTimeout(() => {
    requestLocation()
  }, 500)
})

watch([userCoords, recommendedDrinks, sweetness, addOnLevel], () => {
  if (userCoords.value) {
    generateMapIframe()
  }
})
</script>

<template>
  <div id="app">
    <header class="hero-section">
      <div>
        <p class="eyebrow">智慧飲料尋寶地圖</p>
        <h1>用「當下心情」與「熱量預算」找到附近命定飲料</h1>
        <p class="subtitle">多選需求、即時熱量、Google Maps 導航與步行熱量比例，一次串接完成。</p>
      </div>
    </header>

    <section class="panel">
      <h2>1. 當下需求多選</h2>
      <p class="hint">選擇你現在想要的效果與口味，系統將自動篩出最適合的飲料。</p>
      <div class="selector-grid">
        <div>
          <h3>你現在想要</h3>
          <div class="chips">
            <button
              v-for="option in needsOptions"
              :key="option.value"
              :class="['chip', { active: selectedNeeds === option.value } ]"
              type="button"
              @click="toggleSelection('need', option.value)"
            >
              {{ option.label }}
            </button>
          </div>
        </div>
        <div>
          <h3>偏好口味</h3>
          <div class="chips">
            <button
              v-for="option in flavorOptions"
              :key="option.value"
              :class="['chip', { active: selectedFlavors === option.value } ]"
              type="button"
              @click="toggleSelection('flavor', option.value)"
            >
              {{ option.label }}
            </button>
          </div>
        </div>
      </div>
    </section>

    <section class="panel">
      <h2>2. 熱量預算篩選</h2>
      <p class="hint">滑動甜度與加料選擇，右側即時計算推薦飲料熱量。</p>
      <div class="range-panel">
        <div class="range-control">
          <label>甜度：{{ sweetnessLabels[sweetness] }}</label>
          <input type="range" min="0" max="4" step="1" v-model.number="sweetness" />
          <div class="range-value">+{{ sweetnessValues[sweetness] }} 大卡</div>
        </div>
        <div class="range-control">
          <label>加料級別：{{ addOnLevels[addOnLevel].label }}</label>
          <input type="range" min="0" max="3" step="1" v-model.number="addOnLevel" />
          <div class="range-value">{{ selectedAddOns.length ? selectedAddOns.join('、') : '無加料' }}</div>
        </div>
      </div>
    </section>

    <section class="panel recommendations">
      <div class="section-head">
        <div>
          <h2>3. 精準保留至少三款飲料</h2>
          <p class="hint">{{ message }}</p>
        </div>
        <div class="button-group">
          <button type="button" class="location-btn" @click="requestLocation">📍 定位並加載地圖</button>
          <button type="button" class="test-btn" @click="useTestCoordinates">🧪 使用測試座標</button>
        </div>
      </div>

      <div class="recommendation-list">
        <article v-for="drink in recommendedDrinks" :key="drink.id" class="card">
          <div class="card-title">
            <span class="brand">{{ drink.brand }}</span>
            <h3>{{ drink.name }}</h3>
          </div>
          <div class="tag-row">
            <span class="tag" v-for="tag in drink.tags" :key="tag">{{ tag }}</span>
          </div>
          <div class="card-content">
            <p>基底熱量：{{ drink.baseCalories }} 大卡</p>
            <p>甜度額外：{{ sweetnessValues[sweetness] }} 大卡</p>
            <p>加料熱量：{{ selectedAddOns.reduce((sum, item) => sum + addOnCalories[item], 0) }} 大卡</p>
            <p class="highlight">預估總熱量：{{ currentCalories(drink) }} 大卡</p>
          </div>
          <div class="calorie-grade">
            <p>{{ getCalorieGrade(currentCalories(drink)).emoji }} {{ getCalorieGrade(currentCalories(drink)).sin }}</p>
            <p>{{ getCalorieGrade(currentCalories(drink)).exercise }}</p>
          </div>
          <div class="card-actions">
            <div class="badge">建議：{{ selectedAddOns.length ? selectedAddOns.join('、') : '保持清爽' }}</div>
            <button type="button" class="add-cart-btn" @click="addToCart(drink)">➕ 加入點單車</button>
          </div>
        </article>
      </div>
    </section>

    <section class="panel map-panel">
      <div class="map-info">
        <h2>4. 附近地圖導航</h2>
        <p class="hint">於地圖上顯示附近販售這三款飲料的實體門市，並附帶步行消耗熱量比例。</p>
        <div class="store-list">
          <article v-for="store in recommendedStores" :key="store.id" class="store-card" :class="{ 'store-active': store.matchedTags.length > 0 }">
            <div class="store-header">
              <h3>{{ store.name }}</h3>
              <div v-if="store.matchedTags.length > 0" class="match-badge">
                <span v-for="tag in store.matchedTags" :key="tag" class="match-tag">{{ tag }}</span>
              </div>
            </div>
            <p>販售：{{ store.drink }}</p>
            <p>距離：
              <span v-if="userCoords">
                {{ Math.round(distanceKm(userCoords, store.coords) * 1000) }} 公尺
              </span>
              <span v-else>定位中...</span>
            </p>
            <p v-if="userCoords">
              估計步行消耗：{{ walkCalories(distanceKm(userCoords, store.coords)) }} 大卡
              ({{ walkRatio(store.calories || 1, distanceKm(userCoords, store.coords)) }}%)
            </p>
          </article>
        </div>
      </div>
      <div class="map-frame">
        <p v-if="mapError" class="map-note">⚠️ {{ mapError }}</p>
        <iframe 
          v-else-if="mapIframeSrc"
          :src="mapIframeSrc"
          class="map-embed"
          style="border: 0; border-radius: 12px;"
          allowfullscreen=""
          loading="lazy"
          referrerpolicy="no-referrer-when-downgrade"
        ></iframe>
        <div v-else class="map-placeholder">🗺️ 請點擊「📍 定位並加載地圖」按鈕來顯示地圖</div>
      </div>
    </section>

    <section class="panel cart-section">
      <div class="section-head">
        <h2>🛒 點單車 ({{ cartItems.length }})</h2>
        <button v-if="cartItems.length > 0" type="button" class="copy-btn" @click="copyOrderToClipboard">
          📋 一鍵複製訂單
        </button>
      </div>
      <div v-if="cartItems.length === 0" class="empty-cart">
        <p>購物車為空，請點擊飲料卡片上的「➕ 加入點單車」來新增品項。</p>
      </div>
      <div v-else class="cart-list">
        <div v-for="item in cartItems" :key="item.id" class="cart-item">
          <div class="item-info">
            <h4>{{ item.brand }} - {{ item.name }}</h4>
            <p>
              <span class="detail">{{ item.sweetness }}</span>
              <span v-if="item.addOns.length" class="detail">{{ item.addOns.join('、') }}</span>
              <span class="calories">{{ item.totalCalories }} 大卡</span>
            </p>
          </div>
          <button type="button" class="remove-btn" @click="removeFromCart(item.id)">🗑️</button>
        </div>
        <div class="cart-summary">
          <p>
            <strong>總熱量：</strong>
            {{ cartItems.reduce((sum, item) => sum + item.totalCalories, 0) }} 大卡
          </p>
          <p class="summary-tip">
            {{
              cartItems.reduce((sum, item) => sum + item.totalCalories, 0) <= 1000
                ? '✅ 熱量控制得不錯！'
                : cartItems.reduce((sum, item) => sum + item.totalCalories, 0) <= 1500
                  ? '⚠️ 熱量偏高，記得多運動！'
                  : '🔥 今天熱量超標，明天要節制呢！'
            }}
          </p>
        </div>
      </div>
    </section>
  </div>
</template>
