+++
title = "Love"
description = "Zikey Vi"
date = "2022-05-07"
author = "zkv"

+++




<div align="center">
    <br>
<div class="timer" style="font-size: 32px;">
    【 <b id="d"></b> 天 <b id="h"></b> 时 <b id="m"></b> 分 <b id="s"></b> 秒 】
</div>
<script>
    function timer() {
        var start = new Date(2017, 8, 30);
        var t = new Date() - start;
        var h = ~~(t / 1000 / 60 / 60 % 24);
        if (h < 10) {
            h = "0" + h;
        }
        var m = ~~(t / 1000 / 60 % 60);
        if (m < 10) {
            m = "0" + m;
        }
        var s = ~~(t / 1000 % 60);
        if (s < 10) {
            s = "0" + s;
        }
        document.getElementById('d').innerHTML = ~~(t / 1000 / 60 / 60 / 24);
        document.getElementById('h').innerHTML = h;
        document.getElementById('m').innerHTML = m;
        document.getElementById('s').innerHTML = s;
    }
    timer();
    setInterval(timer, 1000);
</script>

</div>