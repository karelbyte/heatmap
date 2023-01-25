<!-- eslint-disable no-unused-vars -->
<script>

import { Overlay } from 'trading-vue-js'

const valueToHex = (color) => {
    let hexadecimal = color.toString(16);
    return hexadecimal.length == 1 ? "0" + hexadecimal : hexadecimal;
}

const rgbToHex = (red, green, blue) => {
    return "#" + valueToHex(red) + valueToHex(green) + valueToHex(blue);
}

export default {
    name: 'HeatMap',
    mixins: [Overlay],
    methods: {
        meta_info() {
            return {
                author: 'Karel Puerto',
                version: '0.0.1'
            }
        },
        draw(ctx) {
            const layout = this.$props.layout
           
            let y = 0
            let x = 0
            let colorFill = 'black'
            const data = this.$props.data
            data.forEach((candle, index) => {
                ctx.beginPath()
                const [t, c, h, l, n, o, v, vw] = candle
                
                if (index > 0) {
                    if (c >= data[index - 1][1]) {
                        for (let d = 0; d < n; d++) {
                            colorFill = rgbToHex(255, 255, 200 - Math.ceil(v + d));
                            ctx.strokeStyle = colorFill
                            x = layout.t2screen(t) - Math.cos(t) * 2
                            ctx.fillStyle = colorFill
                            y = layout.$2screen(h) - (v / d)
                            ctx.fillRect(x, y, n / o, n * o);
                            ctx.stroke();
                        }

                    } else {
                        for (let u = 0; u < n; u++) {
                            colorFill = rgbToHex(0, 0, 200 - Math.ceil(v * u));
                            ctx.strokeStyle = colorFill
                            x = layout.t2screen(t) - Math.sin(t)
                            ctx.fillStyle = colorFill;
                            y = layout.$2screen(l) + (v / u)
                            ctx.fillRect(x, y, n / vw, n * vw);
                            ctx.stroke();
                        }
                        
                    }
                }


            });
        },
        use_for() { return ['HeatMap'] },
    },
}
</script> 