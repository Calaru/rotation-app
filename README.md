# rotaset
手動選択やランダム生成に対応した組み合わせローテーション管理アプリ
<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">

<title>ローテーション管理</title>

<style>
body{
    font-family:sans-serif;
    max-width:700px;
    margin:auto;
    padding:15px;
    background:#f5f5f5;
}

h1{
    text-align:center;
}

.card{
    background:#fff;
    border-radius:10px;
    padding:15px;
    margin-bottom:15px;
    box-shadow:0 2px 5px rgba(0,0,0,.1);
}

textarea{
    width:100%;
    height:180px;
    box-sizing:border-box;
}

input[type="number"]{
    width:80px;
}

button{
    width:100%;
    padding:12px;
    border:none;
    border-radius:8px;
    background:#2563eb;
    color:white;
    font-size:16px;
}

button:hover{
    background:#1d4ed8;
}

.hidden{
    display:none;
}

.option-item{
    display:block;
    margin:6px 0;
}

.result{
    font-size:22px;
    font-weight:bold;
    line-height:1.8;
}
</style>
</head>

<body>

<h1>ローテーション管理</h1>

<div class="card">
    <h3>候補入力（1行1候補）</h3>

    <textarea id="candidates">A
B
C
D
E
F
G
H</textarea>
</div>

<div class="card">
    <h3>1セットの個数</h3>

    <input type="number" id="setSize" value="4" min="1">
</div>

<div class="card">

    <h3>選択方式</h3>

    <label class="option-item">
        <input type="radio" name="mode" value="manual" checked>
        手動
    </label>

    <label class="option-item">
        <input type="radio" name="mode" value="random">
        完全ランダム
    </label>

    <label class="option-item">
        <input type="radio" name="mode" value="first">
        最初のみ指定
    </label>

</div>

<div class="card" id="selectArea">

    <h3>セット指定</h3>

    <div id="manualCandidates"></div>

</div>

<div class="card">

    <button onclick="generateSet()">
        生成
    </button>

</div>

<div class="card">

    <h3>結果</h3>

    <div id="result" class="result">
        未生成
    </div>

</div>

<script>

function getCandidates(){

    return document
        .getElementById("candidates")
        .value
        .split("\n")
        .map(v => v.trim())
        .filter(v => v !== "");

}

function createManualOptions(){

    const candidates = getCandidates();

    let html = "";

    candidates.forEach(item => {

        html += `
        <label class="option-item">
            <input
                type="checkbox"
                class="manual-check"
                value="${item}">
            ${item}
        </label>
        `;

    });

    document.getElementById(
        "manualCandidates"
    ).innerHTML = html;

}

function updateModeUI(){

    const mode = document.querySelector(
        'input[name="mode"]:checked'
    ).value;

    const selectArea =
        document.getElementById(
            "selectArea"
        );

    if(mode === "random"){

        selectArea.classList.add(
            "hidden"
        );

    }else{

        selectArea.classList.remove(
            "hidden"
        );

    }

}

function randomSelect(list,count){

    const pool = [...list];

    const result = [];

    for(let i = 0;i < count;i++){

        const index =
            Math.floor(
                Math.random() * pool.length
            );

        result.push(pool[index]);

        pool.splice(index,1);

    }

    return result;

}

function generateSet(){

    const candidates =
        getCandidates();

    const count =
        Number(
            document.getElementById(
                "setSize"
            ).value
        );

    if(candidates.length < count){

        alert(
            "候補数が不足しています"
        );

        return;

    }

    const mode =
        document.querySelector(
            'input[name="mode"]:checked'
        ).value;

    let result = [];

    // 手動

    if(mode === "manual"){

        result =
        [...document.querySelectorAll(
            ".manual-check:checked"
        )]
        .map(v => v.value);

        if(result.length !== count){

            alert(
            `${count}個選択してください`
            );

            return;

        }

    }

    // 完全ランダム

    if(mode === "random"){

        result =
        randomSelect(
            candidates,
            count
        );

    }

    // 最初のみ指定

    if(mode === "first"){

        result =
        [...document.querySelectorAll(
            ".manual-check:checked"
        )]
        .map(v => v.value);

        if(result.length !== count){

            alert(
            `${count}個選択してください`
            );

            return;

        }

    }

    document.getElementById(
        "result"
    ).innerHTML =
        result.join("<br>");

}

document
.getElementById("candidates")
.addEventListener(
    "input",
    createManualOptions
);

document
.querySelectorAll(
'input[name="mode"]'
)
.forEach(radio => {

    radio.addEventListener(
        "change",
        updateModeUI
    );

});

createManualOptions();
updateModeUI();

</script>

</body>
</html>
