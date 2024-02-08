# War-game-in-java
//A java code of a war gambling card game
import java.io.FileWriter;
import java.io.IOException;
import java.util.Arrays;
import java.util.Collections;
import java.util.Scanner;

interface Wagerable {
   void placeWager(int amount);
}

//Make a card
class Card {
    private String suit;
    private String rank;

    public Card(String suit, String rank) {
        this.suit = suit;
        this.rank = rank;
    }

    public String toString() {
        return rank + " of " + suit;
    }
}

//Make the deck of the cards
class Deck {
    private Card[] cards;
    private int topCardIndex;

    public Deck(int numDecks) {
        initializeDeck(numDecks);
    }

    private void initializeDeck(int numDecks) {
	String[] suits = {"Hearts", "Diamonds", "Clubs", "Spades"}
	String[] ranks = {"2", "3", "4", "5", "6", "7", "8", "9", "10", "Jack", "Queen", "King", "Ace"};

	cards = new Card[numDecks * 52];
	topCardIndex = 0;

	int index = 0;
	for (int i = 0; i < numDecks; i++) {
	    for (String suit : suits) {
		for (String rank : ranks) {
		    cards[index++] = new Card(suit, rank);
		}
	    }
	}

	shuffle();
    }

    private void shuffle() {
	Collections.shuffle(Arrays.asList(cards));
    }

    public Card drawCard() {
        return cards[topCardIndex++];
    }
}

//When a player makes a decision
class Player implements Wagerable {
    private String name;
    private int chips;
    private int wager;
    private Card hand;
 
    public Player(String name, int chips) {
	this.name = name;
	this.chips = chips;
    }

    public void dealCard(Card card) {
	this.hand = card;
    }

    public void placeWager(int amount) {
	this.wager = Math.min(amount, chips);
    }

    public int getWager() {
	return wager;
    }

    public Card getHand() {
	return hand;
    }

    public void updateChips(int amount) {
	chips += amount;
    }
 
    public String getName() {
	return name;
    }
}

//When the game is on
class game {
    private Player player;
    private Deck deck;

    public Game(String playerName, int startingChips, int numDecks) {
	player = new Player(playerName, startingChips);
	deck = new Deck(numDecks);
    }

    public void playRound() {
	Card playerCard = deck.drawCard();
	Card dealerCard = deck.drawCard();

	player.dealCard(playerCard);

	int result = playerCard.rank.compareTo(dealerCard.rank);

	if (result > 0) {
	    player.updateChips(player.getWager());
	    System.out.println(player.getName() + " wins this round");
	} else if (result < 0) {
		player.updateChips(-player.getWager());
		System.out.println(player.getName() + " loses this round");
	} else {
	    System.out.println("It's a tie, so start the war");
	}
    }

    public void saveOutcomeToFile(String filename) {
	try (FileWriter writer = new FileWriter(filename)) {
	    writer.write("player: " + player.getName() + "\n");
	    writer.write("Total winnings: " + player.getChips());
	} catch (IOException e) {
	    e.printStackTrace();
	}
    }
}

//The app
public class WarGameApp {
    public static void main(String[] args) {
	Scanner scanner = new Scanner(System.in);

	System.out.println("Enter your name:");
	String playerName = scanner.nextLine();

	System.out.println("Enter the number of decks to use (1 for single or 6 for a standard casino one");
	int numDecks = scanner.nextInt();

	System.out.println("Enter the number of starting chips:");
	int startingChips = scanner.nextInt();

	Game warGame = new Game(playerName, startingChips, numDecks);

	while (true) {
	    System.out.println("\nOptions:");
	    System.out.println("1. play the round");
	    System.out.println("2. exit game");

	    int choice = scanner.nextInt();

	    switch (choice) {
		case 1:
		    System.out.println("Enter your wager:");
		    int wagerAmount = scanner.nextInt();
		    warGame.placePlayerWager(wagerAmount);

		    warGame.playRound();
		    break;
	
		case 2;
		   warGame.saveOutcomeToFile("game_outcome.txt");
		   System.out.println("Game outcome saved to game_outcome.txt");
		   scanner.close();
		   System.exit(0);

		default:
		    System.out.println("Invalid option. Try again.");
	   }
	}
    }
}

    
