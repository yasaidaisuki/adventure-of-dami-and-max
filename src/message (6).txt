//Name: Tyler Zeng
//Date: December 6, 2021
//Assignment: Unit 3 Assignment Question 2
//Description: Finds the number of top 20 word occurence in text file

//ASSUMPTIONS:
//APOSTROPHE S IS REMOVED FROM THE WORD. CONTRACTIONS SUCH AS "HE'S" AND "SHE'S" ARE CONSIDERED ONE WORD
//SYMBOLS ARE REMOVED FROM BEGINNING AND END OF WORDS
//NUMBERS ARE NOT COUNTED

import java.util.*;
import java.io.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

import javax.swing.*;

public class u3a2q1 implements ActionListener, Runnable {
	// Global variables
	// Frame
	static JFrame frame = new JFrame("u3a2q1");

	// Panel
	static JPanel mainPanel = new JPanel();
	static JPanel leftPanel = new JPanel();
	static JPanel userIn = new JPanel();
	static JPanel textBox = new JPanel();

	// Right		
	static JPanel rightPanel = new JPanel(); // main
	static JPanel wordDis = new JPanel(); // words main
	static JPanel leftW = new JPanel(); // words
	static JPanel rightW = new JPanel(); // frequency
	static JPanel info = new JPanel(); // top info

	// Buttons
	static JButton addButton = new JButton("Add file");

	// Combo box
	static HashSet<String> textFiles = new HashSet<>();
	static JComboBox<String> comboBox = new JComboBox<String>();

	// Text area
	static JTextArea text = new JTextArea();
	static JScrollPane scroll = new JScrollPane(text, JScrollPane.VERTICAL_SCROLLBAR_ALWAYS,
			JScrollPane.HORIZONTAL_SCROLLBAR_NEVER);

	// HashMap for logic
	static HashMap<String, Integer> allWords = new HashMap<>();

	// Timer
	static long stop;
	static long start;

	////////////////////////////////////////////////////////// METHODS
	////////////////////////////////////////////////////////// //////////////////////////////////////////////////////////
	// Method name: u3a2q1
	// Description: Constructor which creates the JFrame
	// Parameters: n/a
	// Returns: n/a
	public u3a2q1() {
		// Window preferences
		frame.setPreferredSize(Toolkit.getDefaultToolkit().getScreenSize());
		frame.setFocusable(true);

		// Panel preferences
		mainPanel.setLayout(new GridLayout(1, 1));
		leftPanel.setLayout(new BoxLayout(leftPanel, BoxLayout.Y_AXIS));
		userIn.setLayout(new BoxLayout(userIn, BoxLayout.Y_AXIS));
		textBox.setLayout(new BorderLayout());

		// Right panels
		rightPanel.setLayout(new BoxLayout(rightPanel, BoxLayout.Y_AXIS));
		wordDis.setLayout(new GridLayout(1, 1));
		info.setLayout(new FlowLayout());
		leftW.setLayout(new BoxLayout(leftW, BoxLayout.Y_AXIS));
		rightW.setLayout(new BoxLayout(rightW, BoxLayout.Y_AXIS));

		// Add stuff to left panel
		userIn.add(addButton);
		userIn.add(comboBox);
		textBox.add(scroll);
		leftPanel.add(userIn);
		leftPanel.add(textBox);

		// Add sub-panel to main panel
		mainPanel.add(leftPanel);
		mainPanel.add(rightPanel);

		// Action listeners
		addButton.setActionCommand("add");
		addButton.addActionListener(this);

		// ComboBox preferences
		comboBox.addActionListener(this);

		// Text area
		text.setEditable(false);

		// Add panel to frame
		frame.add(mainPanel);
		frame.pack();
		frame.setVisible(true);
	}

	// Method name: actionPerformed
	// Description: Does a task based on the action performed
	// Parameters: Action event
	// Returns: void
	@Override
	public void actionPerformed(ActionEvent ae) {
		String action = ae.getActionCommand();
		// Adds the files from the users computer
		if (action.equals("add")) {
			FileDialog openDialog = new FileDialog(frame, "Open a new file", FileDialog.LOAD);
			openDialog.setVisible(true);
			String fileName = openDialog.getDirectory() + openDialog.getFile();
			// Checks if the file directory is valid and if there is no duplicates
			if (openDialog.getDirectory() != null && textFiles != null
					&& !textFiles.contains(fileName.substring(fileName.lastIndexOf("\\") + 1))) {
				comboBox.addItem(fileName.substring(fileName.lastIndexOf("\\") + 1));
				textFiles.add(fileName.substring(fileName.lastIndexOf("\\") + 1));
			}

		}
		
		// The action of the dropdown menu/JCombo box
		else {
			JComboBox comboBox = (JComboBox) ae.getSource();
			String f = (String) comboBox.getSelectedItem();
			// Input from file
			try {
				
				// Removes all panel to be resetted
				info.removeAll();
				leftW.removeAll();
				rightW.removeAll();
				// Displaying onto the left panel
				BufferedReader input1 = new BufferedReader(new FileReader(f));
				text.read(input1, null);
				input1.close();
				// Finding the number of unique words
				BufferedReader input = new BufferedReader(new FileReader(f));
				// Logic stuff
				String line = "";
				start = System.currentTimeMillis();
				while ((line = input.readLine()) != null) {
					StringTokenizer st = new StringTokenizer(line, " !@#$%^&*()=_+\"\\|]}[{;:,<.>/?`~1234567890");
					while (st.hasMoreTokens()) {
						String in = st.nextToken().toLowerCase();
						// Extraneous cases
						if (in.indexOf("'") != -1) {
							// Contractions
							if (!in.equals("that's")&&!in.equals("he's") && !in.equals("she's") && !in.equals("there's") && !in.equals("it's")
									&& in.substring(in.indexOf("'") + 1).equals("s")) {
								in = in.substring(0, in.length() - 2);
							}
						}
						// Removing stuff/symbols from the end and beginning
						while (!Character.isLetter(in.charAt(0)) && in.length() > 1) {
							in = in.substring(1);
						}
						while (!Character.isLetter(in.charAt(in.length() - 1)) && in.length() > 1) {
							in = in.substring(0, in.length() - 1);
						}
						// Adding to hashmap
						if (allWords.containsKey(in) && !in.equals("") && Character.isLetter(in.charAt(0))) {
							allWords.put(in, allWords.get(in) + 1);
						} else {
							allWords.put(in, 1);
						}
					}
				}
				input.close();
				
				// Sets the keyset and value collection
				Collection<Integer> wordV = allWords.values();
				Iterator<String> wordS = allWords.keySet().iterator();
				LinkedList<word> sorted = new LinkedList<>();
				
				// Adds the keyset and value colletion into linked list of word objects
				for (Integer i : wordV) {
					sorted.add(new word(wordS.next(), i));
				}
				
				// Sort the linked list
				Collections.sort(sorted);
				
				// Display
				info.add(new JLabel("Total time: " + (System.currentTimeMillis() - start) + " milliseconds"));
				info.add(new JLabel(" "));
				info.add(new JLabel(" "));
				info.add(new JLabel("20 Most Frequent Words"));
				info.add(new JLabel(" "));
				info.add(new JLabel(" "));
				leftW.add(new JLabel("Words"));
				rightW.add(new JLabel("Frequency"));

				// Put onto JPanel
				try {
					for (int i = 0; i < 20; i++) {
						leftW.add(new JLabel((i + 1) + ")   " + sorted.getFirst().getWord()));
						rightW.add(new JLabel("" + sorted.removeFirst().getFreq()));
						leftW.add(new JLabel(" "));
						rightW.add(new JLabel(" "));
					}
				} catch (NoSuchElementException e) {
				}
				wordDis.add(leftW);	
				wordDis.add(rightW);
				rightPanel.add(info);
				rightPanel.add(wordDis);
				mainPanel.add(rightPanel);
				mainPanel.revalidate();
				mainPanel.repaint();
				allWords = new HashMap<>();
			} catch (FileNotFoundException e) {
				System.out.println("File does not exist");
				text.setText("");
			} catch (IOException e) {
				System.out.println("Reading error");
			}
		}
	}

	// Main method!!!
	public static void main(String[] args) {
		// Import two default files
		comboBox.addItem("Select File!");
		comboBox.addItem("MOBY.TXT");
		comboBox.addItem("ALICE.TXT");
		comboBox.addItem("input.TXT");
		textFiles.add("MOBY.TXT");
		textFiles.add("ALICE.TXT");
		textFiles.add("input.txt");
		new u3a2q1();
	}

	public void run() {
	}
}