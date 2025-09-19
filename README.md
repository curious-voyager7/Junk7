
# MATLAB Lab Experiments

## 1. Histogram Equalization

### MATLAB Script

```matlab
inputFolder='C:\Users\Soclab2-13\Documents\126018022MATLAB\archive\images\applefruit';
outputFolder='C:\Users\Soclab2-13\Documents\126018022MATLAB\histe';
outputSize=[256,256];

if ~exist(outputFolder,'dir')
    mkdir(outputFolder);
end

imagefiles=dir(fullfile(inputFolder,'*.*'));
validext={'.jpg','.jpeg','.png','.bmp','.tif','.tiff'};

for i=1:length(imagefiles)
    [~,~,ext]=fileparts(imagefiles(i).name);
    if ismember(lower(ext),validext)
        imgpath=fullfile(inputFolder,imagefiles(i).name);
        img=imread(imgpath);
        img=imresize(img,outputSize);
        
        if size(img,3)==1
            imgeq=histeq(img);
        else
            imgeq=img;
            for c=1:3
                imgeq(:,:,c)=histeq(img(:,:,c));
            end
        end
        
        outputpath=fullfile(outputFolder,imagefiles(i).name);
        imwrite(imgeq,outputpath);
        fprintf('Processed and saved:%s\n',imagefiles(i).name);
    end
end
```

### App Designer

```matlab
% Callbacks that handle component events
methods (Access = private)

    % Button pushed function: BrowseImageButton
    function BrowseImageButtonPushed2(app, event)
        global l;
        [filename,filepath]=uigetfile({'*.*,*.jpg;*.png;*.oct;*.bmp'},'select file to open');
        image=[filepath,filename];
        l=imread(image);
        imagesc(app.UIAxes,l);
    end

    % Callback function: HistogramEqButton, UIAxes2
    function HistogramEqButtonPushed2(app, event)
        global l;
        if size(l,3)==1
            o=histeq(l);
        else
            o=l;
            for c=1:3
                o(:,:,c)=histeq(l(:,:,c));
            end
        end
        imagesc(app.UIAxes2,o)
    end
end
```

---

## 2. Image Smoothening Filters

```matlab
i=imread('dog.jfif');
imshow(i);

j=imnoise(i,'salt & pepper');
figure, imshow(j);
title('portion of image with salt and pepper noise');

i=imread('eight.tif');
figure, imshow(i)
title('Image without noise');

j=imnoise(i,'salt & pepper',0.02);
figure, imshow(j)
title('image with salt pepper noise');

kavg=filter2(fspecial('average',3),j)/255;
figure, imshow(kavg)
title('portion of image with average filter');

kmed=medfilt2(j);
imshowpair(kavg,kmed,'montage')
title('image with mean and median filters')
```

---

## 3. Image Sharpening

```matlab
i=imread("rice.jpg");
figure,imshow(i);

a=imsharpen(i);
figure,imshow(a);
```

---

## 4. Plotting Methods

### Line Plot

```matlab
tbl=readtimetable("rainfall.csv");
tbl=sortrows(tbl);
head(tbl,3)
p=plot(tbl,"rainfall");
```

### Scatter Plot

```matlab
data=readtable('rainfall.csv');
scatter(data,'date','temperature');
```

### Bubble Chart

```matlab
bu=readtable('rainfall.csv');
bubblechart(bu,'rainfall','temperature','humidity');
```

### Histogram

```matlab
h=readtable('rainfall.csv');
histogram(h.rainfall);
title("histogram of rainfall");
```

### Box Plot

```matlab
t=readtable("rainfall.csv");
boxchart(t.rainfall,t.temperature)
```

### Pie Chart

```matlab
piee=readtable("rainfall.csv");
h=head(piee,5);
piechart(h.temperature)
```

### Word Cloud

```matlab
w='dog.txt';
d=fileread(w);
punctuation_characters = ["." "?" "!" "," ";" ":"];
d = replace(d, punctuation_characters, " ");
words = split(join(d));
C = categorical(words);
wordcloud(C);
```

### Bar Plot

```matlab
t=readtable("rainfall.csv");
bar(t.rainfall)
```

### Horizontal Bar Plot

```matlab
b=readtable("rainfall.csv");
barh(b.temperature)
```

### Geoplot

```matlab
tl=readtable("world_country_and_usa_states_latitude_and_longitude_values.csv");
hh=head(tl,50)
h=geoplot(hh,"latitude","longitude");
geobasemap topographic
```

---

## 5. Morphological Operations

### App Designer

```matlab
% Callbacks that handle component events
methods (Access = private)

    % Button pushed function: imageButton
    function imageButtonPushed(app, event)
        global l;
        [filename,filepath]=uigetfile({'*.*;*.jpg;*.jpeg;*.png;*.oct;*.bmp;*.jfif'},'select file to open');
        image=[filepath,filename];
        l=imread(image);
        imagesc(app.UIAxes,l);
    end

    % Button pushed function: dilateButton
    function dilateButtonPushed(app, event)
        global l;
        se = strel("line", 7, 7);
        d= imdilate(l, se);
        subplot(2, 3, 2), imshow(d);
        title("Dilated image");
        imagesc(app.UIAxes2,d);
    end

    % Button pushed function: erodedButton
    function erodedButtonPushed(app, event)
        global l;
        se = strel("line", 7, 7);
        e= imerode(l, se);
        subplot(2, 3, 3), imshow(e);
        title("Eroded image");
        imagesc(app.UIAxes3,e);
    end
end
```

---

## 6. Thresholding

```matlab
i=imread("coin.png");
imshow(i);
title("orig image");

g=rgb2gray(i);
level=multithresh(g);
seg=imquantize(g,level);

figure,imshow(seg,[]);
title("after thresholding");
```

---

## 7. Color Image Processing

### App Designer

```matlab
% Callbacks that handle component events
methods (Access = private)

    % Button pushed function: imgButton
    function imgButtonPushed(app, event)
        global l;
        [filename,filepath]=uigetfile({'*.*;*.jpg;*.jpeg;*.png;*.oct;*.bmp;*.jfif'},'select file to open');
        image=[filepath,filename];
        l=imread(image);
        imagesc(app.UIAxes,l);
    end

    % Button pushed function: redButton
    function redButtonPushed(app, event)
        global l;
        r = size(l, 1);
        c = size(l, 2);
        R = zeros(r, c, 3);
        R(:, :, 1) = l(:, :, 1);
        red=uint8(R);
        imagesc(app.UIAxes2,red);
    end

    % Button pushed function: greenButton
    function greenButtonPushed(app, event)
        global l;
        r = size(l, 1);
        c = size(l, 2);
        G = zeros(r, c, 3);
        G(:, :, 2) = l(:, :, 2);
        gr=uint8(G);
        imagesc(app.UIAxes3,gr);
    end

    % Button pushed function: blueButton
    function blueButtonPushed(app, event)
        global l;
        r = size(l, 1);
        c = size(l, 2);
        B = zeros(r, c, 3);
        B(:, :, 3) = l(:, :, 3);
        b=uint8(B);
        imagesc(app.UIAxes4,b);
    end
end
```
