<template>
    <div class="min-h-screen bg-gray-100">
        <div class="container md:py-6 mx-auto">
            <div class="bg-white p-4 shadow rounded">
                <div class="md:flex -mx-4 items-center mb-20">
                    <div class="flex-1 px-4 mb-10 md:mb-0">
                        <select @change="getSpecificSura" class="quran-input" name="" id="">
                            <option value="">Select Sura</option>
                            <option v-for="sura in surah" :value="sura.number">{{ sura.name }} - {{ sura.englishName }}
                            </option>
                        </select>
                    </div>
                    <div class="flex-1 px-4 text-center">
                        <h3 class="font-bold mb-1 text-lg">{{ currentSura.name }} - {{ currentSura.englishName }}</h3>
                        <p>Ayahs - {{ currentSura.numberOfAyahs }} | {{ currentSura.revelationType }}</p>
                    </div>
                </div>
                <div v-if="loading" class="flex items-center text-center w-full justify-center my-10">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                        stroke="currentColor" class="w-6 h-6 animate-spin">
                        <path stroke-linecap="round" stroke-linejoin="round"
                            d="M4.5 12a7.5 7.5 0 0015 0m-15 0a7.5 7.5 0 1115 0m-15 0H3m16.5 0H21m-1.5 0H12m-8.457 3.077l1.41-.513m14.095-5.13l1.41-.513M5.106 17.785l1.15-.964m11.49-9.642l1.149-.964M7.501 19.795l.75-1.3m7.5-12.99l.75-1.3m-6.063 16.658l.26-1.477m2.605-14.772l.26-1.477m0 17.726l-.26-1.477M10.698 4.614l-.26-1.477M16.5 19.794l-.75-1.299M7.5 4.205L12 12m6.894 5.785l-1.149-.964M6.256 7.178l-1.15-.964m15.352 8.864l-1.41-.513M4.954 9.435l-1.41-.514M12.002 12l-3.75 6.495" />
                    </svg>
                    loading ...
                </div>
                <div v-else class="text-4xl">
                    <div v-if="currentSura.hasOwnProperty('ayahs')" v-for="ayah in currentSura.ayahs">
                        <p class="flex items-center justify-end text-right my-6">
                            {{ ayah.text }}
                            <span class="text-xs w-6 h-6 text-center leading-5 border rounded-full ml-4">
                                {{ ayah.numberInSurah }}
                            </span>
                        </p>
                        <hr>
                        <p class="text-lg my-3">
                            ➼ {{ ayah.englishText }}
                        </p>
                        <p class="text-lg my-3">
                            ➼ {{ ayah.banglaText }}
                        </p>
                        <hr>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
export default {
    name: 'App',

    data() {
        return {
            surah: [],
            currentSura: [],
            loading: true
        }
    },

    mounted() {
        // get all surah
        axios
            .get('https://api.alquran.cloud/v1/surah')
            .then(response => {
                this.surah = response.data.data
            });

        // get a specific sura
        this.querySpecificSura(1);
    },

    methods: {
        // get a specific sura
        getSpecificSura(e) {
            this.querySpecificSura(e.target.value);
        },

        querySpecificSura(suraNumber) {
            this.loading = true;
            axios.get('https://api.alquran.cloud/v1/surah/' + suraNumber + '/editions/ar,en.asad,bn.bengali')
                .then(response => {

                    let ayahsArabic = response.data.data[0].ayahs;
                    let ayahsEnglish = response.data.data[1].ayahs;
                    let ayahsBangla = response.data.data[2].ayahs;
                    for (let index = 0; index < ayahsArabic.length; index++) {
                        ayahsArabic[index].englishText = ayahsEnglish[index].text;
                        ayahsArabic[index].banglaText = ayahsBangla[index].text;
                        console.log(ayahsArabic[index], index);
                    }

                    this.currentSura = response.data.data[0];
                    this.loading = false;
                })
        }
    }
};
</script>