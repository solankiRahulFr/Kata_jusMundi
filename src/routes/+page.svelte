<script>
// Variable assignment
  let inputNumbers = "";
  let notNumber = false;
  let errorMsg = "";
  let results = [];
  let numsArray = [];

//   Setting the basic cardinal french numbers with key as number and values as words 
  const cardinalNumbers = {
    1: "un",
    2: "deux",
    3: "trois",
    4: "quatre",
    5: "cinq",
    6: "six",
    7: "sept",
    8: "huit",
    9: "neuf",
    10: "dix",
    11: "onze",
    12: "douze",
    13: "treize",
    14: "quatorze",
    15: "quinze",
    16: "seize",
    17: "dix-sept",
    18: "dix-huit",
    19: "dix-neuf",
    20: "vingt",
    30: "trente",
    40: "quarante",
    50: "cinquante",
    60: "soixante",
    80: "quatre-vingts",
    100: "cent",
    1000: "mille",
    1000000: "million",
  };

  // function to handle the 2 digit number to word conversion
  const moreTwetyTwoDigit = (num) => {
    // input param - 2 digit number 
    // check weather the number belongs to irregular 70s, 90s or 80s row if not then form the word by concatinating cardinalNumber from the object. 
    let wordTwo = "";
    if (num.toString()[0] === "7") {
      wordTwo += `soixante-${num % 10 === 1 ? "et-" + cardinalNumbers[10 + (num % 10)] : cardinalNumbers[10 + (num % 10)]}`;
    } else if (num.toString()[0] === "9") {
      wordTwo += `quatre-vingt-${num % 10 === 1 ? "et-" + cardinalNumbers[10 + (num % 10)] : cardinalNumbers[10 + (num % 10)]}`;
    } else if (num.toString()[0] === "8") {
      wordTwo += `quatre-vingt-${num % 10 === 1 ? "et-" + cardinalNumbers[num % 10] : cardinalNumbers[num % 10]}`;
    } else if (num < 9) {
      wordTwo += cardinalNumbers[num];
    } else {
      if (cardinalNumbers[num]) wordTwo += cardinalNumbers[num];
      else
        wordTwo += `${cardinalNumbers[parseInt(num / 10, 10) * 10]}-${num % 10 === 1 ? "et-" + cardinalNumbers[num % 10] : cardinalNumbers[num % 10]}`;
    }
    return wordTwo;
  };

  // Function to handle 3 digit numbers 
  const biggerThan99 = (num) => {
     // input param - 3 digit number 
     // check if the number hundred places are bigger than 20 to call 2 digit number lower than 20 to call cardinal number directly and 0 to return straight word
    if (num % 100 === 0)
      return `${cardinalNumbers[parseInt(num / 100, 10)]}-cent`;
    else if (num % 100 > 20)
      return `${cardinalNumbers[parseInt(num / 100, 10)]}-cent-${moreTwetyTwoDigit(num % 100)}`;
    else if (num % 100 < 21)
      return `${cardinalNumbers[parseInt(num / 100, 10)]}-cent-${cardinalNumbers[num % 100]}`;
  };

  // Function to handle 4 digit numbers
  const biggerThan999 = (num) => {
    // input param - 3 digit number 
    // cronologicaly varifying the sub-digits and calling the respective above function on the part of the digits if satisfys
    let res = "";
    if (parseInt(num / 1000, 10) < 21)
      res += cardinalNumbers[parseInt(num / 1000, 10)];
    else if (parseInt(num / 1000, 10) > 20 && parseInt(num / 1000, 10) < 100)
      res += moreTwetyTwoDigit(parseInt(num / 1000, 10));
    else res += biggerThan99(parseInt(num / 1000, 10));

    if (num % 1000 === 0) return `${res}-mille`;
    else if (num % 1000 < 100)
      return `${res}-mille-${moreTwetyTwoDigit(num % 1000)}`;
    else if (num % 1000 > 99) return `${res}-mille-${biggerThan99(num % 1000)}`;
  };

  // Handler to validate the input and conver the string to the array of number
  // validating that the entered aray are number and not letters
  // brackets routines are ordered
  // if is valid than converting the numbers to array of words
  const convertWordsHanlder = () => {
    // triming the string for withspace removal
    let numbers = inputNumbers.trim();
    // vaerifying bracket order id present
    if (numbers[0] === "[") {
      console.log(numbers[numbers.length - 1]);
      if (numbers[numbers.length - 1] === "]") {
        numbers = numbers.slice(1, -1);
        notNumber = false;
      } else {
        errorMsg = "Forgot to close the brackets";
        notNumber = true;
      }
    }
    // parsing the string to convert to array of numbers
    numsArray = numbers.split(",").map((item) => {
      if (isNaN(parseInt(item, 10))) {
        notNumber = true;
        errorMsg = "List should only contain comma(,) separated numbers";
      } else {
        notNumber = false;
        errorMsg = "";
        return parseInt(item, 10);
      }
    });
    // if valid coversion by calling respective function on digit count
    if (!notNumber) {
      results = numsArray.map((number) => {
        if (number == 0) return "zero";
        else if (number < 21) return cardinalNumbers[number];
        else if (number > 20 && number < 100) return moreTwetyTwoDigit(number);
        else if (number > 99 && number < 1000) return biggerThan99(number);
        else if (number > 999 && number < 1000000) return biggerThan999(number);
      });
    }
  };
</script>

<div class="main">
  <h3>Kata.</h3>
  <textarea
    id="inputNumbers"
    bind:value={inputNumbers}
    placeholder="Enter number or list of numbers ex: 22, 34 or [22] or [22, 33]"
    class:error={notNumber}
  ></textarea>
  {#if notNumber}
    <span class="errorMsg">{errorMsg}</span>
  {/if}

  <button on:click={convertWordsHanlder}>Get French words</button>
  <div>
    <table class="styled-table">
      <tr>
        <th>Numbers</th>
        <th>French words</th>
      </tr>
      {#if numsArray.length > 0 && results.length > 0}
        {#each numsArray as item, index}
          <tr>
            <td>{item}</td>
            <td>{results[index]}</td>
          </tr>
        {/each}
      {/if}
    </table>
  </div>
</div>

<style>
  .main {
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    font-family: sans-serif;
  }

  .main textarea {
    width: 80%;
    height: 5rem;
    margin-bottom: 1rem;
    font-size: 0.9em;
    padding: 0.5rem;
    border: 1px solid #b1b1b1;
    border-radius: 5px;
  }
  .main button {
    padding: 0.6em 1em;
    border: 1px solid #b1b1b1;
    border-radius: 5px;
    cursor: pointer;
  }
  .styled-table {
    margin: 25px 0;
    font-size: 0.9em;
    min-width: 400px;
    box-shadow: 0 0 15px rgba(0, 0, 0, 0.12);
  }
  .styled-table thead tr {
    color: #ffffff;
    text-align: center;
  }
  .styled-table th,
  .styled-table td {
    padding: 0.6em 0.7em;
    text-align: center;
  }

  textarea.error {
    border-color: red;
  }

  .errorMsg {
    margin-bottom: 1rem;
    font-size: 0.9em;
    color: red;
  }
</style>
