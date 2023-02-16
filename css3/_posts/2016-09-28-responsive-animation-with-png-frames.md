---
title: 自适应png帧动画
---



    /**
     * 人物
     **/
    .miner {
        position: absolute;
        top: 0;
        right: 0;
        bottom: 0;
        left: 0;
        width: px2rem(109px);
        height: px2rem(275px);
        margin: auto;
        background-repeat: no-repeat;
        background-size: px2rem(218px) px2rem(275px);
    }
    .miner-male {
        background-image: url("//gw.alicdn.com/tps/TB1WS7jNpXXXXbyXXXXXXXXXXXX-218-275.png");
    }
    .miner-female {
        background-image: url("//gw.alicdn.com/tps/TB1YHUkNpXXXXbqXXXXXXXXXXXX-218-275.png");
    }

    .miner-animated {
        animation: miner .8s steps(2) infinite;
    }
    @keyframes miner {
        100% {
            background-position: -1 * px2rem(218px);
        }
    }
