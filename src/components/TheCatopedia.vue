<script>
export default {
    data() {
        return {
            API_KEY: '&api_key=live_o06gJnmR2h77OBzbMtpS5aV77EpsJAmRs6OjbCneIQk3w4FDpKYeIj00HrYtkgmC',
            limitOfCat: 12,
            catSearch: '',
            catResults: [],
            selectedCatResults: [],
            selectedCatImage: '',
            selectedCatImageWidth: '',
            selectedCatImageHeight: '',
            breedDetail: false,
            loadMoreButton: false,
        };
    },
    methods: {
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
    watch: {},
    mounted() {
        this.breedOfCat();
        // console.log(this.API_KEY)
        // console.log(process.env.API_KEY)
        // console.log(import.meta.env.BASE_URL_API)
    },
}
</script>
<template>
    <div class="uk-section uk-section-muted">
        <div class="uk-container uk-container-expand">
            <div v-if="!breedDetail">
                <form uk-grid>
                    <div class="uk-width-1-1">
                        <input class="uk-input uk-form-large uk-border-rounded" type="text" placeholder="Search here..."
                            v-model="catSearch" />
                    </div>
                    <p class="uk-margin-medium-bottom">{{ catSearch }}</p>
                </form>
            </div>
            <div v-if="!breedDetail"
                class="uk-child-width-1-2@s uk-child-width-1-3@m uk-child-width-1-3@l uk-child-width-1-4@xl"
                uk-grid="masonry: true;">
                <div v-for="cat in catResults" :key="cat.id">
                    <div class="uk-card uk-card-default uk-border-rounded" style="cursor: pointer"
                        @click="breedOfCatDetail(cat.id)">
                        <div class="uk-card-media-top">
                            <img v-if="cat.image" class="uk-border-rounded" :src="cat.image.url" :width="cat.image.width"
                                :height="cat.image.height" :alt="cat.name" :title="cat.name" />
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
            <div v-if="!breedDetail" class="uk-text-center uk-margin-large-top">
                <button :class="{ loadMoreHidden: loadMoreButton }"
                    class="uk-button uk-button-primary uk-button-large uk-border-rounded" @click="loadMore()">
                    Load more
                </button>
            </div>
        </div>
    </div>
</template>