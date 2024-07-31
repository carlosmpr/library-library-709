---
  title: 'JavaTestFile.java'
  description: 'component description'
  pubDate: 'July 31, 2024'
  author: 'Carlos Polanco'
  ---
  
  
  
  # JavaTestFile.java
  # BMI Chart Explanation

The provided code is a Java program that generates a BMI (Body Mass Index) trend chart using the JFreeChart library. Below is a breakdown of the code:

### Libraries Used
- **JFreeChart**: A popular Java library for creating a variety of charts including pie charts, bar charts, line charts, etc.
- **Swing**: Java's GUI toolkit for creating graphical user interfaces.

### Class: BMIChart
- **Extends ApplicationFrame**: BMIChart class extends the ApplicationFrame class, which is a Swing container that can be used as a top-level window.
- **Constructor BMIChart(String title)**: Initializes the BMIChart with a specified title. Inside the constructor, it creates a JFreeChart and sets up the chart panel to display the chart.

### Method: createDataset()
- **Return Type: XYSeriesCollection**: This method creates a dataset containing two XYSeries (data points for two persons) with their corresponding BMI values over time (in months).

### Method: createChart(XYSeriesCollection dataset)
- **Parameters: XYSeriesCollection dataset**: Takes the dataset created by createDataset() method.
- **Return Type: JFreeChart**: This method creates an XYLineChart (line chart) using the dataset provided. It sets the chart title, axis labels, and orientation.

### Method: main(String[] args)
- **SwingUtilities.invokeLater()**: Ensures that the GUI is created and updated on the Event Dispatch Thread (EDT) to prevent any concurrency issues.
- **Creates an instance of BMIChart**: Sets up the BMIChart window with a title, size, location, close operation, and makes it visible.

### Example Usage
```java
public static void main(String[] args) {
    SwingUtilities.invokeLater(() -> {
        BMIChart example = new BMIChart("BMI Trend Chart");
        example.setSize(800, 600);
        example.setLocationRelativeTo(null);
        example.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
        example.setVisible(true);
    });
}
```

### Running the Code
When executed, the program will display a BMI trend chart showing the Body Mass Index trends of two persons over a period of time (in months). The chart will have the BMI values on the y-axis, time (in months) on the x-axis, and separate lines for each person's BMI trend.

This code provides a simple example of how to create a basic line chart using JFreeChart in Java for visualizing BMI trends.
  
  ## Component Code
  ```jsx
  import org.jfree.chart.ChartFactory;
import org.jfree.chart.ChartPanel;
import org.jfree.chart.JFreeChart;
import org.jfree.chart.plot.PlotOrientation;
import org.jfree.chart.plot.XYPlot;
import org.jfree.chart.renderer.xy.XYLineAndShapeRenderer;
import org.jfree.data.xy.XYSeries;
import org.jfree.data.xy.XYSeriesCollection;
import org.jfree.ui.ApplicationFrame;

import javax.swing.*;
import java.awt.*;

public class BMIChart extends ApplicationFrame {

    public BMIChart(String title) {
        super(title);
        JFreeChart xylineChart = createChart(createDataset());
        ChartPanel chartPanel = new ChartPanel(xylineChart);
        chartPanel.setPreferredSize(new Dimension(800, 600));
        setContentPane(chartPanel);
    }

    private XYSeriesCollection createDataset() {
        XYSeries series1 = new XYSeries("Person 1");
        series1.add(1, 22.5);
        series1.add(2, 23.0);
        series1.add(3, 22.8);
        series1.add(4, 23.5);

        XYSeries series2 = new XYSeries("Person 2");
        series2.add(1, 25.0);
        series2.add(2, 25.4);
        series2.add(3, 25.1);
        series2.add(4, 26.0);

        XYSeriesCollection dataset = new XYSeriesCollection();
        dataset.addSeries(series1);
        dataset.addSeries(series2);

        return dataset;
    }

    private JFreeChart createChart(XYSeriesCollection dataset) {
        JFreeChart chart = ChartFactory.createXYLineChart(
                "BMI Trends",
                "Time (Months)",
                "BMI",
                dataset,
                PlotOrientation.VERTICAL,
                true, true, false);

        XYPlot plot = chart.getXYPlot();
        XYLineAndShapeRenderer renderer = new XYLineAndShapeRenderer();
        plot.setRenderer(renderer);
        return chart;
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            BMIChart example = new BMIChart("BMI Trend Chart");
            example.setSize(800, 600);
            example.setLocationRelativeTo(null);
            example.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
            example.setVisible(true);
        });
    }
}
  ```
  