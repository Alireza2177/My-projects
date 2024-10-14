

---

# **Keyword Clustering and Visualization for Brain Functional Connectivity Research**

This project demonstrates keyword clustering and visualization using **t-SNE** and **K-Means** on keywords extracted from research papers related to brain functional connectivity based on fMRI images. The visualization is enhanced with borders around clusters to improve readability and interactivity.

## **Overview**

This project aims to provide a clear, interactive visualization of keywords extracted from research papers in the field of brain functional connectivity. By using **SciBERT embeddings**, **t-SNE** for dimensionality reduction, and **K-Means** for clustering, we visualize high-dimensional keyword data in an interpretable 2D space.

### **Key Features**
- **SciBERT Embeddings**: Pre-trained BERT model fine-tuned for scientific papers to generate embeddings for keywords.
- **t-SNE Dimensionality Reduction**: A technique to reduce high-dimensional embeddings to two dimensions for visualization.
- **K-Means Clustering**: A popular clustering method to group keywords into meaningful clusters.
- **Interactive Visualization**: Uses **Plotly** for an interactive plot where users can zoom, pan, and hover to explore clusters and keywords.
- **Improved Readability**: Each keyword is given a border to prevent overlap with neighboring points, enhancing clarity.

## **Installation**

To install the required packages, run the following command:

```bash
pip install -r requirements.txt
```

The required libraries include:
- **transformers** (for loading SciBERT)
- **scikit-learn** (for t-SNE, K-Means)
- **plotly** (for interactive visualization)
- **umap-learn** (optional, for UMAP dimensionality reduction)

## **Usage**

1. **Keyword Extraction**: Provide a text file containing your keywords (one per line).
2. **Generating Embeddings**: Use **SciBERT** to convert keywords into vector embeddings.
3. **t-SNE Dimensionality Reduction**: Reduce the high-dimensional embeddings into 2D for plotting.
4. **K-Means Clustering**: Group similar keywords together using K-Means clustering.
5. **Interactive Visualization**: Explore the results with an interactive scatter plot where you can zoom in on dense areas and view keywords by hovering over each point.

### **Example Usage**

```python
# Run the t-SNE and K-Means clustering, then visualize results
python cluster_and_visualize.py --input keywords.txt --clusters 5
```

## **Visualization Example**

The resulting plot includes clusters of keywords, each point bordered to separate it from neighboring points:

![Example Visualization](example_plot.png)

## **Contributing**

Feel free to submit issues or pull requests for improvements. Contributions that enhance the visualization or optimize performance are especially welcome.

## **License**

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---
