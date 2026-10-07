# Damage Classification in CFRP from FFT Features (MATLAB)

University project at the University of Duisburg-Essen, September 2025.

> **Note:** this was a university project. The dataset was provided by the university and is
> private, and the code is built around it, so neither is published here. This page is a
> high-level description of my approach and what I found.

## Background

Carbon-fibre-reinforced plastics (CFRP) are used in aircraft, cars and wind turbines because they
are light and strong. The problem is that damage inside them is often invisible from the outside.
One way to monitor them is acoustic emission: when damage forms, it sends out a short ultrasonic
signal that sensors on the surface can pick up.

Different types of damage tend to produce signals with different frequency content. So the
question in this project was: if you only look at the frequency spectrum (FFT) of a signal, can a
neural network tell what kind of damage caused it, or whether it's just noise?

## What I did

I implemented the whole pipeline in MATLAB with the Deep Learning Toolbox:

1. **Looking at the data first.** Before training anything, I plotted the average spectrum of
   each class and ran a PCA to see how well the classes separate. This already showed that some
   classes overlap a lot, especially fibre breakage and noise.
2. **Preprocessing.** I held out a stratified validation split from the training data and
   normalised the features using training statistics only, so nothing from the test set leaked
   into training.
3. **Two approaches, same conditions.**
   - **One network for everything:** a single multilayer perceptron (MLP) that predicts the
     class directly.
   - **Two steps:** a first network decides "noise or damage", and a second one decides which
     type of damage it is. I combined both outputs into one probability per class, so the two
     designs could be compared directly.

   Both used the same data split and the same preprocessing.
4. **Evaluation.** I compared them on a held-out test set using accuracy, macro-F1, confusion
   matrices and ROC curves.

## Model design

The input is a plain feature vector rather than an image, so I used fully connected networks
(MLPs) instead of CNNs. Each network has hidden layers with batch normalisation, ReLU and
dropout, a small L2 penalty to reduce overfitting, and a softmax output. In the two-step design,
the first stage gets a smaller network than the others, because "noise or damage" is a simpler
question than telling several damage types apart.

## Results

The single network beat the two-step design in both overall accuracy and macro-F1, and it was
more balanced across the classes.

## What I found

- **Simpler was better here.** On paper the two-step design sounds like it should help, because
  it splits the problem into easier parts. In practice the single, well-regularised network did
  better.
- **Mistakes add up in the two-step version.** If the first network gets "noise or damage"
  wrong, the second one has no way to fix it.
- **Fibre breakage was the hardest class for both.** Its spectrum is spread over a wide range of
  frequencies, much like noise, and that pair caused the most common confusion.

## What I learned

- Working with signal data in the frequency domain instead of with images or tables
- Fitting normalisation on the training data only, and keeping the test set untouched until the
  very end
- Comparing two model designs fairly, with the same data and the same preprocessing
- Why accuracy alone isn't enough when some classes are hard: macro-F1 and the confusion matrix
  show where a model actually struggles

## Tools

MATLAB, Deep Learning Toolbox, Statistics and Machine Learning Toolbox

## Data and code

Both are private. The dataset was provided by the university, and the code is built around it.
Further details can be discussed on request, subject to permission.

## Author

Akash Andichira Sudheesh
