import java.awt.*;
import java.awt.event.*;
import java.io.*;
import java.util.Scanner;
import javax.swing.*;
import javax.swing.event.*;
import javax.swing.filechooser.FileNameExtensionFilter;

public class TextEditor extends JFrame implements ActionListener {

	private static final int WIDTH = 1280;
	private static final int HIGHT = 660;

	private JTextArea textArea;
	private JScrollPane scrollPain;
	private JLabel fontLabel;
	private JSpinner fontSizeSlider;
	private JButton fontColorButton;
	private JComboBox fontBox;

	private JMenuBar menuBar;
	private JMenu fileMenu;
	private JMenuItem openItem;
	private JMenuItem saveItem;
	private JMenuItem exitItem;

	public TextEditor() {
		setUpFrame();
		setUpTextArea();
		setUpScrollPain();
		setUpMenuBar();
		setUpMiddleBar();

		this.setVisible(true);
	}

	private void setUpMiddleBar() {
		setUpFontSizeSlider();
		setUpFontBox();

		fontLabel = new JLabel("Font: ");

		fontColorButton = new JButton("Color");
		fontColorButton.addActionListener(this);

		this.add(fontLabel);
		this.add(fontColorButton);
		this.add(scrollPain);
	}

	private void setUpFontBox() {
		String[] fonts = 
			GraphicsEnvironment.getLocalGraphicsEnvironment()
				.getAvailableFontFamilyNames();

		fontBox = new JComboBox(fonts);
		fontBox.addActionListener(this);
		fontBox.setSelectedItem("Arial");

		this.add(fontBox);
	}

	private void setUpMenuBar() {
		menuBar = new JMenuBar();
		fileMenu = new JMenu("File");
		openItem = new JMenuItem("Open");
		saveItem = new JMenuItem("Save");
		exitItem = new JMenuItem("Exit");

		openItem.addActionListener(this);
		saveItem.addActionListener(this);
		exitItem.addActionListener(this);

		// Add all the item to fileMenu
		fileMenu.add(openItem);
		fileMenu.add(saveItem);
		fileMenu.add(exitItem);

		// Add file menu to menuBar
		menuBar.add(fileMenu);

		this.setJMenuBar(menuBar);
	}

	private void setUpFontSizeSlider() {
		fontSizeSlider = new JSpinner();

		// Setting of fontSizeSlider
		fontSizeSlider.setPreferredSize(new Dimension(50, 25));
		fontSizeSlider.setValue(20);
		fontSizeSlider.addChangeListener(new ChangeListener() {

			@Override
			public void stateChanged(ChangeEvent e) {
				updateFontSize();
			}
		});

		this.add(fontSizeSlider);
	}

	private void setUpScrollPain() {
		scrollPain = new JScrollPane(textArea);

		scrollPain.setPreferredSize(new Dimension(WIDTH - 50, HIGHT - 50));
		scrollPain.setVerticalScrollBarPolicy(ScrollPaneConstants.VERTICAL_SCROLLBAR_ALWAYS);
	}

	private void setUpTextArea() {
		textArea = new JTextArea();

		textArea.setLineWrap(true);
		textArea.setWrapStyleWord(true);
		textArea.setFont(new Font("Arial", Font.PLAIN, 20));
	}

	private void setUpFrame() {
		this.setDefaultCloseOperation(3);
		this.setTitle("Text Editor");
		this.setSize(WIDTH, HIGHT);
		this.setLayout(new FlowLayout());
		this.setLocationRelativeTo(null);
	}

	private void updateFontSize() {
		textArea.setFont(
			new Font(
				textArea.getFont().getFamily(),
				Font.PLAIN,
		 		(int) fontSizeSlider.getValue()
			)
		);
	}

	@Override
	public void actionPerformed(ActionEvent e) {
		if (e.getSource() == fontColorButton)
			changeFontColor();
		else if (e.getSource() == fontBox)
			updateFont();
		else if (e.getSource() == openItem)
			openFile();
		else if (e.getSource() == saveItem)
			saveFile();
		else if (e.getSource() == exitItem)
			exit();
	}

	private void exit() {
		System.exit(0);
	}

	private void updateFont() {
		textArea.setFont(new Font((String) fontBox.getSelectedItem(), Font.PLAIN, textArea.getFont().getSize()));
	}

	private void changeFontColor() {
		JColorChooser colorChooser = new JColorChooser();

		Color color = colorChooser.showDialog(null, "Chose a color", Color.black);

		textArea.setForeground(color);
	}

	private void openFile() {
		JFileChooser fileChooser = new JFileChooser();
		fileChooser.setCurrentDirectory(new File("C:\\Users\\Lakshya Seth\\Desktop"));

		FileNameExtensionFilter filter = new FileNameExtensionFilter("Text File", "txt");
		fileChooser.setFileFilter(filter);

		int response = fileChooser.showOpenDialog(null);

		if (response == JFileChooser.APPROVE_OPTION) {

			File file = new File(fileChooser.getSelectedFile().getAbsolutePath());

			Scanner sc = null;

			try {
				sc = new Scanner(file);
				if (file.isFile()) {
					while (sc.hasNextLine()) {
						String n = sc.nextLine() + "\n";
						textArea.append(n);
					}
				}
			} catch (FileNotFoundException e1) {
				e1.printStackTrace();
			}

			finally {
				sc.close();
			}
		}
	}

	private void saveFile() {
		JFileChooser fileChooser = new JFileChooser();
		fileChooser.setCurrentDirectory(new File("C:\\Users\\Lakshya Seth\\Desktop>"));

		int response = fileChooser.showSaveDialog(null);

		if (response == JFileChooser.APPROVE_OPTION) {
			File file;
			PrintWriter fileOut = null;

			file = new File(fileChooser.getSelectedFile().getAbsolutePath());
			try {
				fileOut = new PrintWriter(file);
				fileOut.println(textArea.getText());
			} catch (FileNotFoundException e1) {
				e1.printStackTrace();
			}

			finally {
				fileOut.close();
			}
		}
	}
}
