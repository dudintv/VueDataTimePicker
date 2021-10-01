<template>
  <label for="cc">Time input </label>
  <input id="cc" type="text" value="" placeholder="HH:mm" ref="dateInput" />
</template>

<script>
export default {
  mounted() {
    var input = this.$refs.dateInput;
    const placeholder = "__:__";
    const placeholderArr = placeholder.split("");
    let result = placeholder.split("");

    var dateInputMask = function dateInputMask(el) {
      el.addEventListener("beforeinput", function (e) {
        const allowedInputs = [":", /\d/];
        if (!!e.data && allowedInputs.every((input) => !e.data.match(input))) {
          e.preventDefault();
          return;
        }
        // now we have only ":" or any number

        if (el.selectionStart > 4 && e.data) {
          e.preventDefault();
          return;
        }

        const pattern = [/[0-2]/, /\d/, ":", /[0-5]/, /\d/];

        for (let i = el.selectionStart; i < el.selectionEnd; i++) {
          result[i] = placeholder[i];
        }

        if (e.data && el.selectionStart === 2) el.selectionStart = 3;

        if (e.data) {
          if (e.data.match(pattern[el.selectionStart])) {
            let prevCursorPos = el.selectionStart + 1;
            if (el.selectionStart === 1 && result[0] === "2" && +e.data > 3) {
              result[1] = "3";
              prevCursorPos += 1;
            } else {
              result[el.selectionStart] = e.data;
            }

            el.value = result.join("");
            el.setSelectionRange(prevCursorPos, prevCursorPos);
          } else {
            if (el.selectionStart === 0 && e.data.match(/\d/)) {
              const prevCursorPos = el.selectionStart + 2;
              result[el.selectionStart] = "0";
              result[el.selectionStart + 1] = e.data;
              el.value = result.join("");
              el.setSelectionRange(prevCursorPos, prevCursorPos);
            }
          }

          e.preventDefault();
        } else {
          // allow delete
          if (e.inputType === "deleteContentBackward" || e.inputType === "deleteContentForward") {
            let prevCursorPos = el.selectionStart;
            if (el.selectionStart === el.selectionEnd) {
              let deleteIndex = null;
              if (e.inputType === "deleteContentBackward") {
                deleteIndex = el.selectionStart > 0 ? el.selectionStart - 1 : null;
                prevCursorPos -= 1;
                if (prevCursorPos < 0) prevCursorPos = 0;
                if (el.selectionStart === 3) {
                  el.deleteIndex -= 1;
                }
              } else if (e.inputType === "deleteContentForward") {
                deleteIndex = el.selectionStart < 5 ? el.selectionStart : null;
                prevCursorPos += 1;
              }
              if (deleteIndex !== null) {
                result[deleteIndex] = placeholder[deleteIndex];
              }
            }
            el.value = result.join("");
            el.setSelectionRange(prevCursorPos, prevCursorPos);
          }

          e.preventDefault();
        }
      });
    };

    dateInputMask(input);
  },
};
</script>

<style>
input {
  font-size: 2rem;
}
</style>
