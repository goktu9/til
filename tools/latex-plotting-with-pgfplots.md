# LaTeX: Creating Professional Plots with PGFPlots

In my academic papers (e.g., TIDFlow), instead of taking screenshots of Excel charts (which results in pixelated/blurry images), I use the **`pgfplots`** package. This allows me to generate high-quality vector graphics directly within the LaTeX code.

### 1. Setup
Include the package in the document preamble:

```latex
\usepackage{pgfplots}
\pgfplotsset{compat=1.18}
```

### 2. Implementation Example
Here is the code I used to create the "Publication Trends" chart in my paper. It uses raw coordinates to plot multiple lines on a single axis.

```latex
\begin{figure}[htbp]
\centering
\begin{tikzpicture}
\begin{axis}[
    width=0.48\textwidth,     % Responsiveness to column width
    xlabel={Year},
    ylabel={Number of Publications},
    xmin=2020, xmax=2024,
    ymin=0, ymax=450,
    xtick={2020,2021,2022,2023,2024},
    legend pos=north west,    % Legend placement inside the chart
    ymajorgrids=true,         % Adds horizontal grid lines
    grid style={dashed, gray!30}
]

% Dataset 1: IEEE Xplore (Blue Square)
\addplot[color=blue, mark=square*, thick] coordinates {
    (2020,49)(2021,108)(2022,145)(2023,259)(2024,382)
};
\addlegendentry{IEEE Xplore}

% Dataset 2: SpringerLink (Red Circle)
\addplot[color=red, mark=*, thick] coordinates {
    (2020,59)(2021,101)(2022,141)(2023,177)(2024,217)
};
\addlegendentry{SpringerLink}

\end{axis}
\end{tikzpicture}
\caption{Rising research trend in Deep Learning-based SLR.}
\label{fig:trend_chart}
\end{figure}
```

### 3. Key Benefits
* **Vector Quality:** The text and lines remain sharp at any zoom level (unlike PNG/JPG).
* **Consistency:** The font style and size automatically match the rest of the document.
* **Version Control:** Since the data is stored as text (coordinates) rather than a binary image file, it is easy to track changes in Git.