<template>
  <div class="weather" v-if="weather.city && weather.text">
    <span>{{ weather.city }}&nbsp;</span>
    <span>{{ weather.text }}&nbsp;</span>
    <span>{{ weather.temp }}℃</span>
    <span class="sm-hidden">&nbsp;{{ weather.windDir }}&nbsp;</span>
    <span class="sm-hidden">{{ weather.windScale }}&nbsp;级</span>
  </div>

  <div class="weather" v-else>
    <span>天气数据获取失败</span>
  </div>
</template>

<script setup>
import { Error } from "@icon-park/vue-next";

// MXNZP 配置
const MX_APP_ID = import.meta.env.VITE_IP_APP_ID;
const MX_APP_SECRET = import.meta.env.VITE_IP_APP_SECRET;

// 和风天气 key
const QKEY = import.meta.env.VITE_QWEATHER_KEY;

const weather = reactive({
  city: null,
  text: null,
  temp: null,
  windDir: null,
  windScale: null,
});

const getCityFromIP = async () => {
  const url = `https://www.mxnzp.com/api/ip/self?app_id=${MX_APP_ID}&app_secret=${MX_APP_SECRET}`;
  const res = await fetch(url);
  return res.json();
};

const getCityId = async (cityName) => {
  const url = `https://geoapi.qweather.com/v2/city/lookup?location=${cityName}&key=${QKEY}`;
  const res = await fetch(url);
  return res.json();
};

const getWeatherNow = async (locationId) => {
  const url = `https://devapi.qweather.com/v7/weather/now?location=${locationId}&key=${QKEY}`;
  const res = await fetch(url);
  return res.json();
};

const loadWeather = async () => {
  try {
    if (!MX_APP_ID || !MX_APP_SECRET) throw "未配置 MXNZP IP API";
    if (!QKEY) throw "未配置和风天气 Key";

    // 1. IP 反查城市
    const ipRes = await getCityFromIP();
    const cityRaw = ipRes.data.city;        // 比如：深圳市
    const city = cityRaw.replace(/市/g, ""); // 变成：深圳

    weather.city = city;

    // 2. 用城市名查和风城市 ID
    const cityRes = await getCityId(city);
    const locationId = cityRes.location[0].id;

    // 3. 获取实时天气
    const w = await getWeatherNow(locationId);
    const now = w.now;

    weather.text = now.text;
    weather.temp = now.temp;
    weather.windDir = now.windDir;
    weather.windScale = now.windScale;
  } catch (err) {
    ElMessage({
      message: "天气数据获取失败：" + err,
      icon: h(Error, { theme: "filled", fill: "#efefef" }),
    });
    console.error(err);
  }
};

onMounted(loadWeather);
</script>
