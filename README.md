### 1. **Image Clustering**

```matlab
he = imread("hestain.png");
imshow(he), title('H&E image');
text(size(he,2),size(he,1)+15, "Image courtesy of Alan Partin, Johns Hopkins University", "FontSize",7,"HorizontalAlignment","right");

numColors = 3;
L = imsegkmeans(he,numColors); 
B = labeloverlay(he,L); 
imshow(B)
title("Labeled Image RGB");

lab_he = rgb2lab(he);
ab = lab_he(:,:,2:3);
ab = im2single(ab);
pixel_labels = imsegkmeans(ab,numColors,"NumAttempts",3); 
B2 = labeloverlay(he,pixel_labels);
imshow(B2)
title("Labeled Image a*b*");

mask1 = pixel_labels == 1; 
cluster1 = he .* uint8(mask1); 
imshow(cluster1) 
title("Objects in Cluster 1");

L = lab_he(:,:,1);
L_blue = L .* double(mask3); 
L_blue = rescale(L_blue);
idx_light_blue = imbinarize(nonzeros(L_blue));
```

### 2. **Image Edge Detection**

```matlab
a = imread('D:\Rice.jpg');
b = rgb2gray(a);
subplot(2,2,1); 
imshow(a);
title('Original Image');

c1 = edge(b,'sobel');
subplot(2,2,2); 
imshow(c1); 
title('Sobel Operator'); 

c2 = edge(b,'prewitt'); 
subplot(2,2,3);
imshow(c2);
title('Prewitt Operator'); 

c3 = edge(b,'roberts'); 
subplot(2,2,4); 
imshow(c3); 
title('Roberts Operator');
```

### 3. **Machine Learning**

```matlab
imds = imageDatastore('/MATLAB Drive/kent-mango', 'IncludeSubfolders', true, 'LabelSource', 'foldernames');
[trainingSet, testSet] = splitEachLabel(imds, 0.8, 'randomized');
countEachLabel(trainingSet);

cellSize = [4 4];
img = imread('/MATLAB Drive/kent-mango/defect/Mango_12_A.JPG');
img = imresize(img, [224 224]);
img = im2gray(img);
[hog_4x4, vis4x4] = extractHOGFeatures(img, 'CellSize', cellSize);
hogFeatureSize = length(hog_4x4);
numImages = numel(trainingSet.Files);
trainingFeatures = zeros(numImages, hogFeatureSize, 'single');

for i = 1:numImages
    img1 = readimage(trainingSet, i);
    img1 = imresize(img1, [224 224]);
    img1 = im2gray(img1);
    trainingFeatures(i, :) = extractHOGFeatures(img1, 'CellSize', cellSize);
end

trainingLabels = trainingSet.Labels;
classifier = fitcnet(trainingFeatures, trainingLabels);

numTest = numel(testSet.Files);
testFeatures = zeros(numTest, hogFeatureSize, 'single');
for j = 1:numTest
    imgTest = readimage(testSet, j);
    imgTest = imresize(imgTest, [224 224]);
    imgTest = im2gray(imgTest);
    testFeatures(j, :) = extractHOGFeatures(imgTest, 'CellSize', cellSize);
end

testLabels = testSet.Labels;
predictedLabels = predict(classifier, testFeatures);
confMat = confusionmat(testLabels, predictedLabels);
confusionchart(confMat);
```

### 4. **Deep Learning**

```matlab
digitDatasetPath = fullfile(matlabroot,'toolbox','nnet','nndemos','nndatasets','DigitDataset');
imds = imageDatastore(digitDatasetPath, 'IncludeSubfolders', true, 'LabelSource', 'foldernames');

numTrainFiles = 750;
[imdsTrain, imdsValidation] = splitEachLabel(imds, numTrainFiles, 'randomize');

inputSize = [28 28 1];
numClasses = 10;

layers = [
    imageInputLayer(inputSize)
    convolution2dLayer(5, 20)
    batchNormalizationLayer
    reluLayer
    fullyConnectedLayer(numClasses)
    softmaxLayer
    classificationLayer
];

options = trainingOptions('sgdm', ...
    'MaxEpochs', 4, ...
    'ValidationData', imdsValidation, ...
    'ValidationFrequency', 30, ...
    'Verbose', false, ...
    'Plots', 'training-progress');

net = trainNetwork(imdsTrain, layers, options);

YPred = classify(net, imdsValidation);
YValidation = imdsValidation.Labels;
accuracy = mean(YPred == YValidation);
```
