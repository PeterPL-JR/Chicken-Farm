// Buttons
var button1 = document.getElementById("button1");
var button2 = document.getElementById("button2");
var button3 = document.getElementById("button3");

// Eggs
var egg = [];
for(var i = 0; i < 3; i++) {
    egg[i] = document.getElementById("egg" + (i + 1));
}

// Counter Labels
var eggsLabel = document.getElementById("eggsLabel");
var goldenLabel = document.getElementById("goldenEggsLabel");

// Counters
var eggs = 0;
var golden = 0;

const eggImage = "<img src='egg.png'>";
const goldenEggImage = "<img src='goldenEgg.png'>";

// Visible of Eggs
var eggVisible = [
    "hidden", "hidden", "hidden"
];

// Levels of Upgrate
var upgrateLevels = [0, 0, 0];
var goldenChance = [15, 15, 15];
var eggSpeed = [900, 900, 900];

// Status of Buttons
var buttonStatus = [
    "upgrade", "buy", "buy"
];

var prices = [
    5, 10, 15, 20, 25,
    30, 50, 75, 100
];

function rotate(index) {
    var rand = Math.floor(Math.random() * 2);
    var img = (rand == 0) ? "<img src='chicken1.png'>" : "<img src='chicken2.png'>";
    document.getElementById("chicken" + index).innerHTML = img;
}

function newEgg(index) {
    if(eggVisible[index - 1] == "hidden") {
        var rand = Math.floor(Math.random() * 2);
        
        if(rand == 1) {
            var eggRand = Math.floor(Math.random() * (goldenChance[index - 1] - 1)) + 1;

            if(eggRand == 4) {
                egg[index - 1].innerHTML = goldenEggImage;
                eggVisible[index - 1] = "golden";
            } else {
                egg[index - 1].innerHTML = eggImage;
                eggVisible[index - 1] = "egg";
            }
        }
    }
}

function refreshEggs() {
    eggsLabel.innerHTML = "<img src='egg.png'> Eggs: " + "<span style='color: #957342;'>" + eggs + "</span>";
    goldenLabel.innerHTML = "<img src='goldenEgg.png'> Golden Eggs: " + "<span style='color: #957342;'>" + golden + "</span>";
}

function pickEgg(index) {
    if(eggVisible[index] == "egg") {
        eggs++;
        refreshEggs();
        
        egg[index].innerHTML = "";
        eggVisible[index] = "hidden";

    } else if(eggVisible[index] == "golden") {
        golden++;
        refreshEggs();

        egg[index].innerHTML = "";
        eggVisible[index] = "hidden";
    }
}

egg[0].onclick = function() {
    pickEgg(0);
}
egg[1].onclick = function() {
    pickEgg(1);
}
egg[2].onclick = function() {
    pickEgg(2);
}

// Button1
button1.onmouseenter = function() {
    setCost(1, true);
}
button1.onmouseout = function() {
    setCost(1, false);
}

// Button2
button2.onmouseenter = function() {
    setCost(2, true);
}
button2.onmouseout = function() {
    setCost(2, false);
}

// Button3
button3.onmouseenter = function() {
    setCost(3, true);
}
button3.onmouseout = function() {
    setCost(3, false);
}

function setSocket(index, active) {
    var visible = (active) ? "visible" : "hidden";
    document.getElementById("button" + index).style.setProperty("visibility", visible);
    document.getElementById("level" + index).style.setProperty("visibility", visible);
}

function setCost(index, active) {
    var status = buttonStatus[index - 1];
    var i = index - 1;

    if(status == "upgrade") {
        var price = prices[upgrateLevels[i]];
        var priceStr = (price > eggs) ? "<span style='color: #695435;'>" + price + "</span>" : price;

        var string = (active) ? "Price: " + priceStr + "<img src='egg.png' class='price'>" : "Level: " + (upgrateLevels[i] + 1);
        document.getElementById("level" + index).innerHTML = string;

    } else if(status == "buy") {
        var priceStr = (golden < 3) ? "<span style='color: #695435;'>3</span>" : "3";

        var string = (active) ? "Price: " + priceStr + " <img src='goldenEgg.png' class='price'>" : "";
        document.getElementById("level" + index).innerHTML = string;
    }
}

button1.onclick = function() {
    if(buttonStatus[0] == "upgrade") {
        upgrate(0);
    } else if(buttonStatus[0] == "buy") {
        buy(0);
    }
    setCost(1, true);
}
button2.onclick = function() {
    if(buttonStatus[1] == "upgrade") {
        upgrate(1);
    } else if(buttonStatus[1] == "buy") {
        buy(1);
    }
    setCost(2, true);
}
button3.onclick = function() {
    if(buttonStatus[2] == "upgrade") {
        upgrate(2);
    } else if(buttonStatus[2] == "buy") {
        buy(2);
    }
    setCost(3, true);
}

function upgrate(index) {
    var button;
    if(index == 0) button = button1;
    else if(index == 1) button = button2;
    else if(index == 2) button = button3;

    if(upgrateLevels[index] == 8) {
        button.style.setProperty("visibility", "hidden");
        document.getElementById("level" + (index + 1)).style.setProperty("visibility", "hidden");
    }
    if(upgrateLevels[index] >= 9) {
        return;
    }

    var price = prices[upgrateLevels[index]];
    if(eggs >= price) {
        eggSpeed[index] -= 60;
        goldenChance[index]--;

        upgrateLevels[index]++;
        eggs -= price;
        refreshEggs();
    }
}

function buy(index) {
    var button;
    if(index == 0) button = button1;
    else if(index == 1) button = button2;
    else if(index == 2) button = button3;

    if(golden >= 3) {
        buttonStatus[index] = "upgrade";
        button.value = "Upgrade";
        document.getElementById("chicken" + (index + 1)).style.setProperty("visibility", "visible");

        setInterval("newEgg(" + (index + 1) + ")", eggSpeed[1]);
        setInterval("rotate(" + (index + 1) + ")", 1000);

        golden -= 3;
        refreshEggs();
    }
}

setInterval("newEgg(1)", eggSpeed[0]);
setInterval("rotate(1)", 1000);
refreshEggs();