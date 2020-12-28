<body>
    <div class="wavy">
        <span style="--i:1;">L</span>
        <span style="--i:2;">O</span>
        <span style="--i:3;">A</span>
        <span style="--i:4;">D</span>
        <span style="--i:5;">I</span>
        <span style="--i:6;">N</span>
        <span style="--i:7;">G</span>
        <span style="--i:8;">.</span>
        <span style="--i:9;">.</span>
        <span style="--i:10;">.</span>
    </div>
</body>

<style>
    *{
        margin: 0;
        padding: 0;
    }
    body{
        padding-top: 50px;
        display: flex;
        justify-content: center;
        align-items: top;
        min-height: 100vh;
    }
    .wavy{
        position: relative;
    }
    .wavy span{
        position: relative;
        display: inline-block;
        font-size: 2em;
        animation: animate 1s ease-in-out infinite;
        animation-delay: calc(0.1s * var(--i));
    }
    @keyframes animate{
        0%{
            transform: translateY(0px);
        }
        20%{
            transform: translateY(-20px);
        }
        40%,100%{
            transform: translateY(0px);
        }
    }
</style>