# Bulls-And-Cows
This is step 6 in the project Bulls and Cows from JetBrains Academy - Basic Java
package bullscows;

import java.util.*;

public class Main {


    public static void main(String[] args) {

        SecretCode codeOfAnotherStage = new SecretCode();
        //Main password = new Main();
        Scanner scanner = new Scanner(System.in);
        System.out.println("Input the length of the secret code");
        int limit = scanner.nextInt();
        System.out.println("Input the number of possible symbols in the code");
        int possibleSymbols = scanner.nextInt();


        String codeWithCharacters = codeOfAnotherStage.sendSecretCode(limit, possibleSymbols);
        codeOfAnotherStage.sendMessage(codeWithCharacters);

        //String code = password.sendPassword(limit);
        //System.out.println(code);


        int turns = 0;
        Grader grad = new Grader(codeWithCharacters);
        boolean flagForTheGame = true;
        while(flagForTheGame) {
            turns++;
            System.out.println(String.format("Turn : %d", turns));
            Scanner scanner1 = new Scanner(System.in);
            String toCheck = scanner1.nextLine();
            grad.grader(toCheck);
            grad.indexVerify();
            ArrayList<Integer> counters = grad.sendCounterOfBullsAndCows();
            grad.sendMessage(counters);
            if (counters.get(0).equals(limit)) {
                flagForTheGame = false;
            }
        }
        System.out.println("Congratulation! You guessed the secret code.");


    }
    public String sendPassword(int limit) {

        List<String> secretCode = new ArrayList<>();
        StringBuilder code = new StringBuilder();
        String answer;
        Random random = new Random();
        while(secretCode.size() < limit) {
            if (limit > 10) {
                break;
            }
            //long pseudoRandomNumber = System.nanoTime();

            long pseudoRandomNumber = random.nextLong();

            ArrayList<String> number = new ArrayList<>(Arrays.asList(String.valueOf(pseudoRandomNumber).split("")));
            ArrayList<String> numberCopy = new ArrayList<>(number);
            ArrayList<String> toRemove = new ArrayList<>();


            for (String value : numberCopy) {
                if (value.equals("0")) {
                    toRemove.add(value);
                }
            }

            numberCopy.removeAll(toRemove);
            if (secretCode.isEmpty()) {
                secretCode.add(numberCopy.get(numberCopy.size() - 1));
            }




            for (int i = number.size() - 1; i >= 0; i--) {


                if (secretCode.size() < limit && !secretCode.contains(number.get(i))) {
                    secretCode.add(String.valueOf(number.get(i)));

                }
            }



        }

        for (String value : secretCode) {
            code.append(value);
        }
        if (limit > 10) {
            answer = String.format("Error: can't generate a secret number with a length of %d because there aren't enough unique digits", limit);

        } else {
            answer = code.toString();

        }
        return answer;
    }


}

class Grader {
    String password;


    // set the password instance variable with the code got
    public Grader(String code) {
        this.password = code;
    }

    // gets the index of the password if it matches with toCheck
    List<Integer> passwordIndex = new ArrayList<>();

    // gets the index of the toCheck if it matches with the password
    List<Integer> toCheckIndex = new ArrayList<>();

    // gets the answer COW or BULL
    List<String> result = new ArrayList<>();

    // Collection that has the number of times BULLS and COWS
    ArrayList<Integer> counters = new ArrayList<>();

    // Counter of times of BULLS
    int countBulls = 0;

    /// Counter of times of COWS
    int countCows = 0;

    // verify if it has the digits
    // and adds the indexes to the corresponding lists
    public void grader(String toCheck) {
        passwordIndex.clear();
        toCheckIndex.clear();
        for (int i = 0; i < this.password.length(); i++) {
            for (int j = 0; j < toCheck.length(); j++) {
                if (this.password.charAt(i) == toCheck.charAt(j)) {

                    this.passwordIndex.add(i);
                    this.toCheckIndex.add(j);
                }
            }
        }


    }

    // verify if the indexes coincide
    public void indexVerify() {
        this.result.clear();
        for (int i = 0; i < passwordIndex.size(); i++) {
            if (passwordIndex.get(i).equals(toCheckIndex.get(i))) {
                result.add("Bull");
            } else {
                result.add("Cow");
            }

        }



    }
    // send the arrays with the countBulls (index 0) and countCows (index 1)
    public ArrayList<Integer> sendCounterOfBullsAndCows() {
        this.counters.clear();
        this.countBulls = 0;
        this.countCows = 0;

        for (String i : this.result) {
            if (i.equals("Cow")) {
                this.countCows++;
            } else {
                this.countBulls++;
            }
        }
        this.counters.add(this.countBulls);
        this.counters.add(this.countCows);
        return this.counters;


    }



    //Send the message
    public void sendMessage(ArrayList<Integer> counters) {
        String message = String.format("Grade: %d bull(s) and ", counters.get(0));
        String message2 = String.format("%d cow(s).", counters.get(1));
        String message3 = String.format(" The secret code is %s.", password);
        System.out.println(message + message2 + message3);
    }


}

// new version of the Code with [0-9] and [a-z]
class SecretCode {
    String alphabet = "abcdefghijklmnopqrstuvwxyz";
    String numbers = "0123456789";
    int lengthOfCharacters;

    public String sendSecretCode(int codeLength, int lengthOfCharacters) {
        this.lengthOfCharacters = lengthOfCharacters;
        Random random = new Random();
        StringBuilder charactersAllowed = new StringBuilder();


        // fill characters allowed StringBuilder
        for (int i = 0; i < lengthOfCharacters; i++) {
            charactersAllowed.append(alphabet.charAt(i));

        }
        StringBuilder base = new StringBuilder(charactersAllowed + this.numbers);
        StringBuilder copy = new StringBuilder(base); // this copy is to be modified instead of the base
        ArrayList<String> disorderBase = new ArrayList<>(); // disorder the elelemnts of the base
        StringBuilder secretCode = new StringBuilder(); // here goes the final code


        for (int i = 0; i < base.length(); i++) {
            int index = random.nextInt(copy.length());
            disorderBase.add(String.valueOf(copy.charAt(index)));
            copy.deleteCharAt(index);


        }
		/* This is for test the code

		System.out.println(String.format("This is the base: %s", base));
		System.out.println(String.format("This is the Stringbuilder: %s", copy));
		System.out.print("This is the ArrayList: ");
		System.out.println(disorderBase);
		System.out.println(String.format("This is the base's length: %d", base.length()));
		System.out.print("This is the disorderBase length: ");
		System.out.println(disorderBase.size());
		*/

        for (int i = 0; i < codeLength; i++) {
            secretCode.append(disorderBase.get(i));
        }


        return secretCode.toString();
    }

    public void sendMessage(String secretCode) {
        System.out.print(String.format("The secret is prepared: %s",  "*".repeat(secretCode.length())));
        String lastCharacter = String.valueOf(this.alphabet.charAt(this.lengthOfCharacters - 1));
        String firstCharacter = String.valueOf(this.alphabet.charAt(0));
        System.out.println(String.format(" (0-9, %s-%s).", firstCharacter, lastCharacter));

    }


}
