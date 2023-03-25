<template>
    <div>
        <div v-if="isPlayerAvailable" id="unity-container" class="unity-desktop">
            <canvas id="unity-canvas" width=960 height=600></canvas>
            <div id="unity-loading-bar">
                <div id="unity-logo"></div>
                <div id="unity-progress-bar-empty">
                    <div id="unity-progress-bar-full"></div>
                </div>
            </div>
            <div id="unity-warning"> </div>
            <div id="unity-footer">
                <div id="unity-webgl-logo"></div>
                <div id="unity-fullscreen-button"></div>
                <div id="unity-build-title">athaeck-about-me-urp</div>
            </div>
        </div>
        <div v-else></div>
    </div>
</template>

<script>


export default {
    props: {
        buildUrl: {
            type: String,
            required: true
        }
    },
    name: 'AhUnityWebPlayer',
    data ()
    {
        return {
            container: null,
            canvas: null,
            loadingBar: null,
            progressBarFull: null,
            fullscreenButton: null,
            warningBanner: null,
            isPlayerAvailable: true
        }
    },
    methods: {
        unityShowBanner ( msg, type )
        {
            const div = document.createElement( 'div' );
            div.innerHTML = msg;
            this.warningBanner.appendChild( div );
            if ( type == 'error' ) div.style = 'background: red; padding: 10px;';
            else
            {
                if ( type == 'warning' ) div.style = 'background: yellow; padding: 10px;';
                setTimeout( function ()
                {
                    this.warningBanner.removeChild( div );
                    this.updateBannerVisibility();
                }, 5000 );
            }
            this.updateBannerVisibility();
        },
        updateBannerVisibility ()
        {
            this.warningBanner.style.display = warningBanner.children.length ? 'block' : 'none';
        }
    },
    mounted ()
    {
        this.container = document.querySelector( "#unity-container" );
        this.canvas = document.querySelector( "#unity-canvas" );
        this.loadingBar = document.querySelector( "#unity-loading-bar" );
        this.progressBarFull = document.querySelector( "#unity-progress-bar-full" );
        this.fullscreenButton = document.querySelector( "#unity-fullscreen-button" );
        this.warningBanner = document.querySelector( "#unity-warning" );

        const loaderUrl = this.buildUrl + "/athaeck-about-me-urp-build.loader.js";
        const config = {
            dataUrl: this.buildUrl + "/athaeck-about-me-urp-build.data",
            frameworkUrl: this.buildUrl + "/athaeck-about-me-urp-build.framework.js",
            codeUrl: this.buildUrl + "/athaeck-about-me-urp-build.wasm",
            streamingAssetsUrl: this.buildUrl + "/StreamingAssets",
            companyName: "athaeck",
            productName: "About me",
            productVersion: "1",
            showBanner: this.unityShowBanner,
        };
        if ( /iPhone|iPad|iPod|Android/i.test( navigator.userAgent ) )
        {
            this.isPlayerAvailable = false
        }
        this.canvas.style.width = "960px";
        this.canvas.style.height = "600px";
        this.loadingBar.style.display = "block";

        var script = document.createElement( "script" );
        script.src = loaderUrl;
        script.onload = () =>
        {
            createUnityInstance( this.canvas, config, ( progress ) =>
            {
                this.progressBarFull.style.width = 100 * progress + "%";
            } ).then( ( unityInstance ) =>
            {
                this.loadingBar.style.display = "none";
                this.fullscreenButton.onclick = () =>
                {
                    unityInstance.SetFullscreen( 1 );
                };
            } ).catch( ( message ) =>
            {
                alert( message );
            } );
        };
        document.body.appendChild( script );

    }
}

</script>
<style scoped>
#unity-container {
    position: absolute
}

#unity-container.unity-desktop {
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%)
}

#unity-container.unity-mobile {
    width: 100%;
    height: 100%
}

#unity-canvas {
    background: #231F20
}

.unity-mobile #unity-canvas {
    width: 100%;
    height: 100%
}

#unity-loading-bar {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    display: none
}

#unity-logo {
    width: 154px;
    height: 130px;
    background: url('assets/images/unity-logo-dark.png') no-repeat center
}

#unity-progress-bar-empty {
    width: 141px;
    height: 18px;
    margin-top: 10px;
    margin-left: 6.5px;
    background: url('assets/images/progress-bar-empty-dark.png') no-repeat center
}

#unity-progress-bar-full {
    width: 0%;
    height: 18px;
    margin-top: 10px;
    background: url('assets/images/progress-bar-full-dark.png') no-repeat center
}

#unity-footer {
    position: relative
}

.unity-mobile #unity-footer {
    display: none
}

#unity-webgl-logo {
    float: left;
    width: 204px;
    height: 38px;
    background: url('assets/images/webgl-logo.png') no-repeat center
}

#unity-build-title {
    float: right;
    margin-right: 10px;
    line-height: 38px;
    font-family: arial;
    font-size: 18px
}

#unity-fullscreen-button {
    float: right;
    width: 38px;
    height: 38px;
    background: url('assets/images/fullscreen-button.png') no-repeat center
}

#unity-warning {
    position: absolute;
    left: 50%;
    top: 5%;
    transform: translate(-50%);
    background: white;
    padding: 10px;
    display: none
}
</style>