<template>
    <div class="message-wrapper" :title="stringTime">
        <div :class="style_class"  :style="styleGenerator" :id="id" v-html="content">
            <!-- Content is inserted via v-html -->
        </div>
    </div>
</template>

<script>
export default {
    name: 'message',
    props: [ 'messageData', 'threadColor' ],

    mounted () {

        this.style_class.push(this.round);

        switch ( this.mime ) {
            case "text/plain": {
                this.content = this.content.replace(/\n/g, "<br />");
                break;
            }
            
            default: {
                this.content = "<i>Not yet supported</i>";
                break;
            }
        }

        switch ( this.type ) {
            case 0:
            case 6: { 
                this.color = this.threadColor;
                this.style_class.push('received');
                break;
            }
            case 3: {
                this.style_class.push('error');
                break;
            }
            case 5: {
                this.style_class.push("info mdl-color-text--grey-700");
                break;
            }
            default: {
                this.style_class.push('sent') //TODO add text color from global theme
            }
        }


    },

    data () {
        return {
            id: this.messageData.device_id,
            mime: this.messageData.mime_type,
            content: this.messageData.data,
            type: this.messageData.message_type,
            timestamp: this.messageData.timestamp,
            color: 'rgba(255,255,255,1)',
            style_class: [ ]
        }
    },

    computed: {
        round () {
            return this.$store.state.round ? 'message-round' : 'message';
        },
        stringTime () {
            return new Date(this.timestamp).toLocaleString()
        },
        styleGenerator () {
            // Only style recieved and media
            if (this.type != 0 && this.type != 6) 
                return "";

            return "background: " + this.color + ";"
                + "border-color: " + this.color
                + " transparent;"
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
    @import "../../assets/scss/_vars.scss";

    .message-wrapper {
        user-select: text;
        clear: both;
        display: block;
    }

    .message, .message-round {
        position: relative;
        padding: 16px;
        margin: 4px 8px 4px 8px;
        max-width: 310px;
        border-radius: 2px;
        overflow-wrap: break-word;
        word-wrap: break-word;
        min-width: 18px;

        &:after {
            content: "";
            position: absolute;
            bottom: -20px;
            left: 50px;
            border-style: solid;
            display: block;
            top: 0px;
            bottom: auto;
        }
    }

    .received {
        float: left;
        color: white;
        margin-left: 28px;
        background: white;
        border-color: red transparent;

        &:after {
            left: -18px;
            border-width: 15px 0 0 20px;
            border-color: inherit;
        }
    }

    .sent {
        float: right;
        color: black;
        margin-right: 28px;
        background: white;

        &:after {
            right: -18px;
            border-width: 15px 20px 0 0;
            border-color: white transparent;
        }
    }
    
    .error {
        float: right;
        color: black;
        margin-right: 28px;
        background: #F44336;
        color: white;

        &:after {
            right: -18px;
            border-width: 15px 20px 0 0;
            border-color: #F44336 transparent;
        }
    }

    .info {
        text-align: center;
        margin: auto;

        &:after {
            border-color: transparent transparent;
        }
    }

</style>