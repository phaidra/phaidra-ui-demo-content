# Demo content

![open access logo](https://raw.githubusercontent.com/phaidra/phaidra-ui-demo-content/main/assets/open-access.jpg)

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Aenean nunc arcu, maximus id dignissim vel, posuere at velit. In tincidunt bibendum nisl, id placerat ipsum efficitur eget.


# Dynamic test

<jsonld-wrapper :pid="'o:2044651'"></jsonld-wrapper>
<script>
    Vue.use(PhaidraVueComponents)

    const i18n = new VueI18n({
        locale: 'eng',
        fallbackLocale: 'eng',
        silentTranslationWarn: true,
        messages
    })

    Vue.component('jsonld-wrapper', {
        name: 'jsonld-wrapper',
        props: {
            pid: String,
            copyrightLink: String
        },
        template: `<v-row><v-col cols="12"><p-d-jsonld :copyright-link="copyrightLink" :jsonld="displayjsonld" :pid="pid"></p-d-jsonld></v-col></v-row>`,
        data() {
            return {
                displayjsonld: {}
            }
        },
        methods: {
            loadMetadata: function (pid) {
                this.loadedMetadata = []
                var self = this
                var url = self.$store.state.instanceconfig.api + '/object/' + pid + '/metadata?mode=resolved'
                var promise = fetch(url, {
                    method: 'GET',
                    mode: 'cors'
                })
                .then(function (response) { return response.json() })
                .then(function (json) {
                    if (json.metadata['JSON-LD']) {
                        return json.metadata['JSON-LD']
                    }
                })
                .catch(function (error) {
                    console.log(error)
                })
                return promise
            },
            loadDisplay: function() {
                this.displayjsonld = {}
                let self = this
                this.loadMetadata(self.pid).then(function (jsonld) { 
                    self.displayjsonld = jsonld 
                })
            }
        },
                                mounted: function () {
            this.$store.commit('setInstanceApi', 'https://services.phaidra.univie.ac.at/api')
            this.$store.commit('setInstancePhaidra', 'phaidra.univie.ac.at')
            this.loadDisplay()
        }
    });

    new Vue({ 
        el: '#app',
        vuetify: new Vuetify({ theme: { themes: { light: { primary: '#008080' } } } }),
        i18n,
        store,
        data: { 
            displayjsonld: {}
        }
    })
</script>
