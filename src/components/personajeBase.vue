<template>
    <v-layout row wrap justify-center>
        <v-flex xs12 md10 xl7>
            <v-card>
                <v-card-text>
                    <v-layout row wrap fill-height align-center>
                        <v-flex xs12 md4 lg3 class="px-3">
                            <v-img height="300" contain
                            :src="character.picture"></v-img>
                        </v-flex>
                        <v-flex xs12 md8 lg9>
                            <v-layout row wrap fill-height align-center style="height: 50px;">
                                <div class="headline font-weight-bold">{{ character.name }}</div>
                                <v-spacer></v-spacer>
                                <v-btn color="gem" flat outline>Follow</v-btn>
                            </v-layout>
                            <div class="body-1 charDescription" v-html="character.description">
                            </div>
                        </v-flex>
                        <v-flex xs12>
                            <v-divider class="my-2"></v-divider>
                            <div class="title font-weight-bold">Related Content</div>

                            <v-layout row class="my-2 myContainer" v-if="1">
                                <v-flex xs5 sm4 md3 xl2 v-for="(aux, index) in holders" :key="index"
                                    style="cursor: pointer;">
                                    <v-layout row wrap align-center fill-height @click="goToHolder (aux)">
                                        <v-img width="150" height="220" class="ma-1"
                                            :src="aux.thumbnail">
                                            <v-layout row wrap fill-height align-end text-xs-center>
                                                <v-flex xs12>
                                                    <v-layout row wrap style="background-color: rgba(0,0,0,0.5);" justify-center>
                                                        <div :class="{'headline': 0, 'subheading': 1, 'white--text': 1}" >
                                                            {{aux.titulo}}
                                                        </div>
                                                    </v-layout>
                                                </v-flex>
                                            </v-layout>
                                        </v-img>
                                    </v-layout>
                                </v-flex>
                            </v-layout>

                        </v-flex>
                        <v-flex xs12>
                            <v-divider class="my-2"></v-divider>
                            <div class="title font-weight-bold">Images</div>
                            <v-layout row class="my-2 myContainer" v-if="loaded">
                                <v-flex xs12 lg4 v-for="(aux, index) in images" :key="index">
                                    <v-img contain width="220" class="mx-2"
                                        :src="aux">
                                    </v-img>
                                </v-flex>
                            </v-layout>
                        </v-flex>
                    </v-layout>
                </v-card-text>
            </v-card>
        </v-flex>
        <v-flex xs12 md10 lg10 xl3>
            <v-card>
                <v-card-text>
                    <v-layout row wrap>
                        <v-flex xs12 class="my-2">
                            <div class="title font-weight-bold">Related characters</div>
                        </v-flex>
                        <v-flex xs12>

                        </v-flex>
                    </v-layout>

                    <v-layout row wrap justify-center>
                        <v-flex xs2 md2 lg2 xl2 v-for="(aux, index) in 13" :key="index"
                            class="ma-1">
                            <v-layout column align-center text-xs-center justify-center>
                                <v-avatar
                                    size="70"
                                    color="red">
                                    <img src="https://i.kym-cdn.com/entries/icons/facebook/000/007/344/Icon.jpg" alt="alt">
                                </v-avatar>
                                <div class="body-1">Kaname Madoka</div>
                            </v-layout>
                        </v-flex>
                    </v-layout>
                </v-card-text>
            </v-card>
        </v-flex>
    </v-layout>
</template>

<script>
export default {
    data () {
        return {
            loaded: false,
            urlImgBase: 'http://localhost/Odr/Characters/',
            images: [],
            character: {
                idPersonaje: '',
                name: '',
                description: '',
                picture: '',
            },
            holders: [
            ]
        }
    },
    mounted () {
        let bodyFormData = new FormData ()
        bodyFormData.set ('urlChar', this.$route.params.urlChar)
        console.log("CHAR", this.$route.params.urlChar)
        this.axios.post('http://localhost/Odr/connections/streamingContent/getCharacterData.php', bodyFormData).then(response => {
            console.log("Response:", response.data)
            this.character.IdPersonaje = response.data.data.IdPersonaje
            this.character.urlChar = response.data.data.URLPersonaje
            this.character.name = response.data.data.NombrePersonaje
            this.character.description = response.data.data.DescripcionPersonaje
            this.character.picture = "http://localhost/Odr/Characters/"+this.character.urlChar+"/profile.jpg"
            this.urlImgBase += this.character.IdPersonaje + "/"

            let cantImages = response.data.data.NumeroImagenes
            for (let i = 1; i <= cantImages; i++) {
                this.images.push('http://localhost/Odr/Characters/' + this.character.urlChar + "/" + i + ".jpg")
            }

            let holdersArray = response.data.related
            if (Array.isArray(holdersArray)){
                let context = this;
                holdersArray.forEach(element => {
                  console.log("ELEMENT:", element)
                    let aux = {
                        idPersonaje: element.Idpersonaje,
                        idSaga: element.IdSaga,
                        idScanHolder: element.IdHolder,
                        categoria: element.NombreCategoria,
                        titulo: element.TituloHolder,
                        urlHolder: element.URLHolder,
                        urlSaga: element.URLSaga,
                        // http://localhost/Odr/Manga/fanmades-bonitos-xd/thumbnail.jpg]
                        thumbnail: "http://localhost/Odr/Manga/"+element.URLHolder+"/thumbnail.jpg"
                        }
                    context.holders.push(aux)
                });
            }
            this.loaded = true
        })
    },
    methods: {
        goToHolder (holder) {
          console.log("HOLDER", holder)
            this.$router.push("/sagas/" + holder.urlSaga + "/" + holder.urlHolder)
        }
    },
    computed: {

    }
}
</script>

<style>
    .myContainer{
        overflow-y: hidden;
        overflow-x: auto;
    }

    .charDescription {
        height: 250px;
        max-height: 251px;
        overflow-y: auto;
    }

</style>
