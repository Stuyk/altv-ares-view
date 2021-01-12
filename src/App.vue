<template>
    <v-app dark>
        <v-main>
            <v-container class="main d-flex flex-column align-content-center align-center justify-center fadein">
                <template v-if="!loading">
                    <v-img
                        class="mb-12"
                        src="~@/assets/discord.svg"
                        min-height="150"
                        min-width="150"
                        max-width="150"
                        max-height="150"
                        block
                    ></v-img>
                    <v-btn @mouseup="beginAuth">Click Here to Authorize with Discord</v-btn>
                    <p class="mt-6">A page will open up outside of your game and assist you with logging in.</p>
                </template>
                <template v-else>
                    <div class="fadein d-flex flex-column align-content-center align-center justify-center">
                        <template v-if="!isPortless">
                            <div class="lds-ring mb-12">
                                <div></div>
                                <div></div>
                                <div></div>
                                <div></div>
                            </div>
                            <p>
                                Tab out and check your browser to finish authentication. <br />If it failed try opening
                                the window again. <a @mousedown="authAgain">Re-open Window.</a>
                            </p>
                        </template>
                        <template v-else>
                            <p>
                                Tab out and check your browser to finish authentication. <br />If it failed try opening
                                the window again. <a @mousedown="authAgain">Re-open Window.</a>
                            </p>
                            <span class="text-sm-subtitle-2 pt-1 pb-5">
                                Note: If pressed before authorization complete. Nothing will happen.
                            </span>
                            <v-btn @mouseup="finishAuth">Finish Authorization</v-btn>
                        </template>
                    </div>
                </template>
                <p class="context mt-6">Ares Discord Authentication - Written by <a @click="loadAthena">Stuyk</a></p>
            </v-container>
        </v-main>
    </v-app>
</template>

<style>
body,
html {
    overflow: hidden !important;
}

.v-application {
    background: url('~@/assets/bg.png') no-repeat center center fixed !important;
    background-size: stretch;
    background-repeat: no-repeat;
}

.main {
    width: 100vw;
    height: 100vh;
}

.context {
    position: fixed;
    bottom: 5vh;
}

.fadein {
    animation: fadein 2s;
}

@keyframes fadein {
    from {
        opacity: 0;
    }
    to {
        opacity: 1;
    }
}

.lds-ring {
    display: inline-block;
    position: relative;
    width: 150px;
    height: 150px;
}

.lds-ring div {
    box-sizing: border-box;
    display: block;
    position: absolute;
    width: 150px;
    height: 150px;
    margin: 8px;
    border: 8px solid #fff;
    border-radius: 50%;
    animation: lds-ring 1.2s cubic-bezier(0.5, 0, 0.5, 1) infinite;
    border-color: #fff transparent transparent transparent;
}

.lds-ring div:nth-child(1) {
    animation-delay: -0.45s;
}

.lds-ring div:nth-child(2) {
    animation-delay: -0.3s;
}

.lds-ring div:nth-child(3) {
    animation-delay: -0.15s;
}

@keyframes lds-ring {
    0% {
        transform: rotate(0deg);
    }
    100% {
        transform: rotate(360deg);
    }
}
</style>

<script>
export default {
    name: 'App',
    data() {
        return {
            url: null,
            loading: false,
            isPortless: false,
            done: false,
            updates: 0
        };
    },
    methods: {
        setAsReady() {
            this.$nextTick(() => {
                this.updates += 1;
            });
        },
        beginAuth() {
            setTimeout(() => {
                this.getURL();
                this.loading = true;
                this.updates += 1;
            }, 100);
        },
        finishAuth() {
            if (!this.isPortless) {
                return;
            }

            if ('alt' in window) {
                alt.emit('discord:FinishAuth');
            }
        },
        authAgain() {
            this.getURL();
        },
        getURL() {
            if ('alt' in window) {
                alt.emit('discord:OpenURL');
            }
        },
        openURL(url, isPortless = false) {
            if (this.window) {
                try {
                    this.window.close();
                } catch (err) {
                    console.log(err);
                }
            }

            this.isPortless = isPortless;
            this.window = window.open(url);
        },
        loadAthena() {
            window.open(`https://github.com/stuyk/altv-athena`);
        },
        finishedLoading() {
            this.$nextTick(() => {
                this.setAsReady();
            });
        },
        endWindow() {
            if (this.window) {
                try {
                    this.window.close();
                } catch (err) {
                    console.log(err);
                }
            }
        }
    },
    mounted() {
        if ('alt' in window) {
            alt.on('discord:OpenURL', this.openURL);
            alt.on('discord:endWindow', this.endWindow);
        }

        this.finishedLoading();
    }
};
</script>
