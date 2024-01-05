<script>
export default {
    data() {
        return {
            API_KEY: import.meta.env.VITE_API_KEY,
            limitOfCat: 12,
            catSearch: '',
            suggestedCatSearch: [],
            catResults: [],
            allCatResults: [],
            selectedCatResults: [],
            selectedCatImage: '',
            selectedCatImageWidth: '',
            selectedCatImageHeight: '',
            breedDetail: false,
            loadMoreButton: false,
            eburImage: [
                'https://cdn2.thecatapi.com/images/d8sbdRtLJ.jpg',
                'https://cdn2.thecatapi.com/images/DZzcGANt5.jpg',
                'https://cdn2.thecatapi.com/images/oLtx9gsxx.jpg'
            ]
        };
    },
    watch: {
        catSearch() {
            let filteredCat = this.allCatResults.filter((item) => {
                let filteredCatName = item.name.toLowerCase().includes(this.catSearch)
                return filteredCatName
            });
            this.suggestedCatSearch = filteredCat
        }
    },
    methods: {
        async listAllCat() {
            const resp = await fetch(`https://api.thecatapi.com/v1/breeds?limit=67${this.API_KEY}`);
            const data = await resp.json();
            let results = data;
            this.allCatResults = results;
        },
        async breedOfCat() {
            const resp = await fetch(`https://api.thecatapi.com/v1/breeds?limit=${this.limitOfCat}${this.API_KEY}`);
            const data = await resp.json();
            let results = data;
            this.catResults = results;
        },
        async breedOfCatDetail(catId) {
            this.breedDetail = true;
            const resp = await fetch(`https://api.thecatapi.com/v1/images/search?breed_ids=${catId}${this.API_KEY}`);
            const data = await resp.json();
            const breedData = data[0];
            if (breedData && breedData.breeds && breedData.breeds.length > 0) {
                this.selectedCatResults = breedData.breeds[0];
                this.selectedCatImage = breedData.url;
                this.selectedCatImageWidth = breedData.width;
                this.selectedCatImageHeight = breedData.height;
            }
            else {
                console.error('Invalid API response format');
            }
        },
        async loadMore() {
            if (this.limitOfCat < 67) {
                this.limitOfCat = this.limitOfCat + 11;
            }
            else {
                this.loadMoreButton = true;
            }
            const resp = await fetch('https://api.thecatapi.com/v1/breeds?limit=' +
                this.limitOfCat +
                this.API_KEY);
            const data = await resp.json();
            let results = data;
            this.catResults = results;
        },
    },
    mounted() {
        this.breedOfCat();
        this.listAllCat();
    },
}
</script>
<template>
    <div class="uk-section uk-section-muted">
        <div class="uk-container uk-container-expand">
            <div v-if="!breedDetail">
                <form uk-grid>
                    <div class="uk-width-1-1">
                        <input class="uk-input uk-form-large uk-border-rounded" type="text"
                            placeholder="Type cat breed here. ex: Balinese, Bengal, Persian etc" v-model="catSearch" />
                    </div>
                    <p class="uk-margin-small-top">
                        <span v-if="catSearch">
                            <span class="uk-text-bold">
                                "{{ catSearch }}"
                            </span> is it
                        </span>
                        <span v-if="catSearch" @click="breedOfCatDetail(i.id)" class="uk-margin-small-right"
                            :class="{ 'uk-link': i.id }" v-for="i in suggestedCatSearch ">
                            {{ i.name }}
                        </span>
                    </p>
                </form>
                <div v-if="catSearch"
                    class="uk-child-width-1-2@s uk-child-width-1-3@m uk-child-width-1-3@l uk-child-width-1-4@xl uk-margin-medium-top"
                    uk-grid="masonry: true;">
                    <div v-for="  cat   in   suggestedCatSearch  " :key="cat.id">
                        <div class="uk-card uk-card-default uk-border-rounded" style="cursor: pointer"
                            @click="breedOfCatDetail(cat.id)">
                            <div class="uk-card-media-top">

                                <div v-if="cat.image">

                                    <img class="uk-border-rounded" :src="cat.image.url" :width="cat.image.width"
                                        :height="cat.image.height" :alt="cat.name" :title="cat.name" />
                                </div>

                            </div>
                            <div class="uk-card-body">
                                <!-- <p>{{ cat.id }}</p> -->
                                <p class="uk-margin-remove uk-text-meta uk-text-primary">
                                    Origin: {{ cat.origin }}
                                </p>
                                <h3 class="uk-card-title uk-margin-small-top uk-text-bold">
                                    {{ cat.name }}
                                </h3>
                                <p>{{ cat.description }}</p>
                            </div>
                            <div class="uk-card-footer">
                                <p class="uk-text-meta uk-margin-remove">
                                    Traits: {{ cat.temperament }}
                                </p>
                            </div>
                        </div>
                    </div>
                </div>
                <div v-else
                    class="uk-child-width-1-2@s uk-child-width-1-3@m uk-child-width-1-3@l uk-child-width-1-4@xl uk-margin-medium-top"
                    uk-grid="masonry: true;">
                    <div v-for="  cat   in   catResults  " :key="cat.id">
                        <div class="uk-card uk-card-default uk-border-rounded" style="cursor: pointer"
                            @click="breedOfCatDetail(cat.id)">
                            <div class="uk-card-media-top">
                                <div v-if="cat.image">

                                    <img class="uk-border-rounded" :src="cat.image.url" :width="cat.image.width"
                                        :height="cat.image.height" :alt="cat.name" :title="cat.name" />
                                </div>

                            </div>
                            <div class="uk-card-body">
                                <!-- <p>{{ cat.id }}</p> -->
                                <p class="uk-margin-remove uk-text-meta uk-text-primary">
                                    Origin: {{ cat.origin }}
                                </p>
                                <h3 class="uk-card-title uk-margin-small-top uk-text-bold">
                                    {{ cat.name }}
                                </h3>
                                <p>{{ cat.description }}</p>
                            </div>
                            <div class="uk-card-footer">
                                <p class="uk-text-meta uk-margin-remove">
                                    Traits: {{ cat.temperament }}
                                </p>
                            </div>
                        </div>
                    </div>
                </div>
                <div v-if="!catSearch" class="uk-text-center uk-margin-large-top">
                    <button :class="{ loadMoreHidden: loadMoreButton }"
                        class="uk-button uk-button-primary uk-button-large uk-border-rounded" @click="loadMore()">
                        Load more
                    </button>
                </div>
            </div>
            <div v-else>
                <div class="uk-margin-large-bottom">
                    <button class="uk-button uk-button-default uk-border-rounded" @click="breedDetail = false">
                        Back
                    </button>
                </div>
                <div class="uk-card uk-card-default uk-border-rounded uk-grid-collapse uk-child-width-1-2@s uk-margin"
                    uk-grid>
                    <div class="uk-card-media-left uk-cover-container">
                        <p>{{ selectedCatResults.id }}</p>
                        <img class="uk-border-rounded" :src="selectedCatImage" :alt="selectedCatResults.name" uk-cover />
                        <canvas :width="selectedCatImageWidth" :height="selectedCatImageHeight">
                        </canvas>
                    </div>
                    <div>
                        <div class="uk-card-body">
                            <p class="uk-card-title uk-text-bold">
                                {{ selectedCatResults.name }}
                            </p>
                            <p class="uk-margin-medium-bottom">
                                {{ selectedCatResults.description }}
                            </p>
                            <p class="uk-text-meta uk-margin-small-bottom">
                                Detailed info
                            </p>
                            <div class="uk-child-width-1-2@s uk-grid-large" uk-grid>
                                <div>
                                    <form action="" class="uk-form-stacked">
                                        <div class="uk-margin">
                                            <label class="uk-form-label" for="adaptability">Adaptability:
                                            </label>
                                            <div class="uk-form-controls">
                                                <progress id="adaptability" class="uk-progress"
                                                    :value="selectedCatResults.adaptability" max="5"></progress>
                                            </div>
                                        </div>
                                        <div class="uk-margin">
                                            <label class="uk-form-label" for="child_friendly">Child
                                                friendliness:
                                            </label>
                                            <div class="uk-form-controls">
                                                <progress id="child_friendly" class="uk-progress"
                                                    :value="selectedCatResults.child_friendly" max="5"></progress>
                                            </div>
                                        </div>
                                        <div class="uk-margin">
                                            <label class="uk-form-label" for="energy_level">Energy level:
                                            </label>
                                            <div class="uk-form-controls">
                                                <progress id="energy_level" class="uk-progress"
                                                    :value="selectedCatResults.energy_level" max="5"></progress>
                                            </div>
                                        </div>
                                        <div class="uk-margin">
                                            <label class="uk-form-label" for="intelligence">Intelligence:
                                            </label>
                                            <div class="uk-form-controls">
                                                <progress id="intelligence" class="uk-progress"
                                                    :value="selectedCatResults.intelligence" max="5"></progress>
                                            </div>
                                        </div>
                                        <div class="uk-margin">
                                            <label class="uk-form-label" for="shedding_level">Shedding level:
                                            </label>
                                            <div class="uk-form-controls">
                                                <progress id="shedding_level" class="uk-progress"
                                                    :value="selectedCatResults.shedding_level" max="5"></progress>
                                            </div>
                                        </div>
                                    </form>
                                </div>
                                <div>
                                    <ul class="uk-list uk-list-divider">
                                        <li>
                                            <p class="uk-margin">
                                                <span class="uk-text-bold">Temperament: <br /></span>
                                                {{ selectedCatResults.temperament }}
                                            </p>
                                        </li>
                                        <li>
                                            <p class="uk-margin">
                                                <span class="uk-text-bold">Origin:</span> {{
                                                    selectedCatResults.origin }}
                                            </p>
                                        </li>
                                        <li>
                                            <p class="uk-margin">
                                                <span class="uk-text-bold">Lifespan:</span> {{
                                                    selectedCatResults.life_span }} years
                                            </p>
                                        </li>
                                        <li>
                                            <p class="uk-margin">
                                                This breed is
                                                <span class="uk-text-bold" v-if="selectedCatResults.indoor > 0">outdoor
                                                    cat</span>
                                                <span class="uk-text-bold" v-else>indoor cat</span>
                                            </p>
                                        </li>
                                        <li>
                                            <p>
                                                More info
                                                <a v-if="selectedCatResults.vcahospitals_url" class="uk-link"
                                                    target="_blank"
                                                    :href="selectedCatResults.vcahospitals_url">{{ selectedCatResults.name }}</a>
                                                <a v-else="selectedCatResults.wikipedia_url" class="uk-link" target="_blank"
                                                    :href="selectedCatResults.wikipedia_url">{{ selectedCatResults.name }}</a>
                                            </p>
                                        </li>
                                    </ul>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="uk-margin-large-top">
                    <button class="uk-button uk-button-default uk-border-rounded" @click="breedDetail = false">
                        Back
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>