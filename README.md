//# Black-Jack-Test
//Entry coding test for codeworks. This is an amalgamation of many different attempts (first attempt at coding anything significant)

let cards = [];
let playerCard = [];
let dealerCard = [];
let cardCount = 0;
let endplay = false;
let suits = ["spades", "hearts", "clubs", "diams"];
let numb = ["A", "2", "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K"];
let message = document.getElementById("message");
let output = document.getElementById("output");
let dealerHolder = document.getElementById("dealerHolder");
let playerHolder = document.getElementById("playerHolder");
let pValue = document.getElementById("pValue");
let dValue = document.getElementById("dValue");


//build the deck of cards with their suits and numbers (in order)
for (s in suits) {
  let suit = suits[s];
  let bgcolor = (suit === "spades" || suit === "clubs") ? "black" : "red";
  for (n in numb) {
//console.log(suits[s] + numb[n]); //if put just suits, gives all four suits four times
//below is how to add special characters (the suits) into the code - add "&"+ before suits[s] and 
// add ";"+ before numb[n]
//output.innerHTML += "<span style='color:" + bgcolor + "'>&" + suits[s] + ";" + numb[n] + "</span> ";
    let cardValue = (n > 9) ? 10 : parseInt(n) + 1
    let card = {
      suit: suit,
      icon: suits[s],
      bgcolor: bgcolor,
      cardnum: numb[n],
      cardvalue: cardValue
    }
    cards.push(card);
  }
}

//gameplay functions
function Start() {
  shuffleDeck(cards);
  dealNew();
  //below is to hide start button once pressed(could put timer on it for more effect)
  $("#start").css("display", "none");
  //non-jQuery code: document.getElementById('start').style.display = 'none';
}

//below function good for shuffling any array not just cards
//Fisher–Yates Shuffle
 function shuffleDeck(array) {
   for (let i = array.length - 1; i > 0; i--) {
     let j = Math.floor(Math.random() * (i + 1));
     let temp = array[i];
     array[i] = array[j];
     array[j] = temp;
   }
  return array;
}

function dealNew() {
 $("#dValue").html("?");
 //dValue.innerHTML = "?";
  playerCard = [];
  dealerCard = [];
  //clearing out the html of the two elements below (housekeeping)
  $("#dealerHolder").html("");
  $("#playerHolder").html("");
  //dealerHolder.innerHTML = "";
  //playerHolder.innerHTML = "";
  
  //document.getElementById('myactions').style.display = 'block';
  $("#myactions").css("display", "block");
  $(".newgame").css("display", "none");
  $("#message").html("Get up to 21 and beat the dealer to win");
  //message.innerHTML = "Get up to 21 and beat the dealer to win.";
  
  deal();
}

function deal(){
   console.log(cards);
  // card count reshuffle
  //ensure both the dealer and the player receive two cards each
  for (let i = 0; i < 2; i++) {
     dealerCard.push(cards[cardCount]);
     dealerHolder.innerHTML += cardOutput(cardCount, i);
     if (i === 0) {
      //need to cover up the dealer's first card
      dealerHolder.innerHTML += '<div id="cover" style="left:100px;"></div>';
     }
     cardCount++
     //do the same thing for the player but without covering the cards
     playerCard.push(cards[cardCount]);
     playerHolder.innerHTML += cardOutput(cardCount, i);
     cardCount++
   }

   pValue.innerHTML = checktotal(playerCard);
   console.log(dealerCard);
  console.log(playerCard);
}

function cardOutput(n, i) {
  //ensure cards are not output on top of each other...basically so we can see 
  //all the cards
  let hpos = (i > 0) ? i * 60 + 100 : 100;
  return '<div class="icard ' + cards[n].icon + '" style="left:' + hpos + 'px;">  <div class="top-card suit">' + cards[n].cardnum + '<br></div>  <div class="content-card suit"></div>  <div class="bottom-card suit">' + cards[n].cardnum +
    '<br></div> </div>';
}

//below are the options provided to the user - hit or hold
function cardAction(a){
  console.log(a);
  switch (a){
    case 'hit':
      playCard(); //if press 'hit' we add a new card to players hand
      break;
   case 'hold':
      playend(); //If press 'hold, go to function playend (playout and calculate)
      break;
  default:
      console.log('done');
      playend(); // playout and calculate
  }
}

//a function to deal with if the player adds to their cards - i.e. 'Hit'
function playCard(){
  //adding a new card to the player array
  playerCard.push(cards[cardCount]);
  playerHolder.innerHTML += cardOutput(cardCount, (playerCard.length - 1));
  cardCount++;
  //checking the value of the player's cards
  let rValu = checktotal(playerCard);
  pValue.innerHTML = rValu;
  if(rValu > 21){
    //if it is over 21...
    message.innerHTML = "oops...bust!";
    playend();
  }
}

//important because the below updates what is displayed to the user
function playend(){
  endplay = true;
  $("#cover").css("display", "none");
  $("#myactions").css("style", "none");
  $("#btndeal").css("display", "block");
  $(".question").css("display", "none");
  $(".newgame").css("display", "block");
  //document.getElementById('cover').style.display = 'none';
  //document.getElementById('myactions').style.display = 'none';
  //document.getElementById('btndeal').style.display = 'block';
  $("message").html("Game Over!")
  //message.innerHTML = "Game Over";
  let dealervalue =  checktotal(dealerCard);
  dValue.innerHTML = dealervalue;

  //below is the dealer check...could be the AI section in some respects
  while(dealervalue < 17){
    dealerCard.push(cards[cardCount]);
    dealerHolder.innerHTML += cardOutput(cardCount, (dealerCard.length - 1));
    cardCount++;
    dealervalue =  checktotal(dealerCard);
    dValue.innerHTML = dealervalue;
  }

  //Who won???
  //end game if player has blackjack
  //(This can be shortened)
  let playervalue =  checktotal(playerCard);
  if(playervalue === 21 && playerCard.length === 2){
    message.innerHTML = "Player Blackjack";
  }
  //outcomes which allow for the player to win
    if((playervalue < 22 && dealervalue < playervalue) || (dealervalue > 21 && playervalue < 22 )){
    message.innerHTML += '<span style="color:green;"> You WIN! </span>';
  }
  //outcomes which allow for the dealer to win
  else if (playervalue === dealervalue) {
    message.innerHTML += '<span style="color:blue;"> A DRAW </span>';
  }
  else {
    message.innerHTML += '<span style="color:red;"> Dealer Wins! </span>';
  }

  dValue.innerHTML = dealervalue;
}

//check to see if the calculation works by seeing if there is an ace and potentially 
//adjusting the total value if need be
function checktotal(arr){
  let rValue = 0;
  let aceAdjust = false;
  for  (let i in arr ){
    if(arr[i].cardnum === 'A' && !aceAdjust){
      aceAdjust = true;
       rValue = rValue + 10;
     }
     rValue = rValue + arr[i].cardvalue;
   }
//if there is an ace and the total is above 21, the total is reduced by 10 to avoid
//the player going bust
   if(aceAdjust && rValue > 21  ){
     rValue = rValue - 10;
   }
   return rValue;
 }
