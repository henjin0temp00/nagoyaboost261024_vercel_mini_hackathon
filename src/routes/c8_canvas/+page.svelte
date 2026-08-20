<script>
    import { onMount } from "svelte";

    let canvas = $state();
    let ctx = $state();

    let drawing = $state(false);

    // 線の色
    let lineColor = $state("#ff0000");

    // 線の太さ
    let lineWidth = $state(5);

    // 現在の設定をCanvasへ反映
    const currentStyle = $derived({
        color: lineColor,
        width: lineWidth
    });

    onMount(() => {
        ctx = canvas.getContext("2d");

        // 背景を白で初期化
        ctx.fillStyle = "#ffffff";
        ctx.fillRect(0, 0, canvas.width, canvas.height);

        ctx.lineCap = "round";
        ctx.lineJoin = "round";
    });

    // 色や太さが変わるたびに反映
    $effect(() => {
        if (!ctx) return;

        ctx.strokeStyle = currentStyle.color;
        ctx.lineWidth = currentStyle.width;
    });

    function start(e) {
        drawing = true;

        ctx.beginPath();
        ctx.moveTo(e.offsetX, e.offsetY);
    }

    function draw(e) {
        if (!drawing) return;

        ctx.lineTo(e.offsetX, e.offsetY);
        ctx.stroke();
    }

    function stop() {
        drawing = false;
    }

    function clearCanvas() {
        ctx.fillStyle = "#ffffff";
        ctx.fillRect(0, 0, canvas.width, canvas.height);
    }
</script>

<div style="margin-bottom: 12px;">
    下の枠内でお絵かきできます
</div>

<div
    style="
        display: flex;
        gap: 16px;
        align-items: center;
        margin-bottom: 12px;
        flex-wrap: wrap;
    "
>
    <label>
        線の色：
        <input
            type="color"
            bind:value={lineColor}
        />
    </label>

    <label>
        線の太さ：
        <input
            type="range"
            min="1"
            max="20"
            bind:value={lineWidth}
        />
        {lineWidth}px
    </label>

    <button onclick={clearCanvas}>
        全部消す
    </button>
</div>

<canvas
    bind:this={canvas}
    width="400"
    height="400"
    style="border: 1px solid black; touch-action: none;"
    onmousedown={start}
    onmousemove={draw}
    onmouseup={stop}
    onmouseleave={stop}
></canvas>