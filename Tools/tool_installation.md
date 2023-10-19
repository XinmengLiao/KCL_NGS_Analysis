#### Homebrew Installation

https://brew.sh/

Follow the instructions on Homebrew offcial website.  

Follow the instructions (if showed) after successful installation.  e.g. 

    **==> Next steps:**

- Run these two commands in your terminal to add Homebrew to your **PATH**:

    (echo; echo 'eval "$(/opt/homebrew/bin/brew shellenv)"') >> /Users/xinmengliao/.zprofile

    eval "$(/opt/homebrew/bin/brew shellenv)"

- Run **brew help** to get started

Test installation by `brew help`

![](/Users/xinmengliao/Library/Application%20Support/marktext/images/2023-10-18-17-23-35-image.png)

#### Tools Installation from Homebrew

1. wget installation: `brew install wget`

2. python installation: `brew install python@3.9`

3. samtools installation: `brew install samtools`

4. fastqc installation: `brew install fastqc`

#### GATK Installation (MacOS)

1. Check the compulsory softwares and version.  
   
   - To run GATK:
     - Java 17 is needed to run or build GATK. We recommend one of the following:
       - Download the Eclipse Foundation's distribution of OpenJDK 17 from [adoptium.net](https://adoptium.net/). Navigate to the [release archive](https://adoptium.net/temurin/archive/?version=17) to find downloads for Java 17.
       - On Mac OS, you can install the [Homebrew package manager](https://brew.sh/) and run `brew tap homebrew/cask-versions` followed by `brew install --cask temurin17` to install the Eclipse Foundation's OpenJDK 17.
     - Python 2.6 or greater (required to run the `gatk` frontend script)
     - Python 3.6.2, along with a set of additional Python packages, is required to run some tools and workflows. See [Python Dependencies](https://github.com/broadinstitute/gatk#python) for more information. 
       - Python could also be installed by homebrew. 
     - R 3.2.5 (needed for producing plots in certain tools)

2. Download the lastest version and unzip [Releases · broadinstitute/gatk · GitHub](https://github.com/broadinstitute/gatk/releases)[Releases · broadinstitute/gatk · GitHub](https://github.com/broadinstitute/gatk/releases)

3. Test installation by `./gatk -help`
   
   ![](/Users/xinmengliao/Library/Application%20Support/marktext/images/2023-10-16-23-50-02-image.png)

4. Create alias for bwa

    `vim ~/.zshrc`

    `alias fastqc="<PATH>/gatk"`

#### 

#### PICARD Installation

1. Download the lastest version of `picard.jar` and unzip https://github.com/broadinstitute/picard/releases/download/3.1.0/picard.jar

2. Test installation by ``
   
   `java -jar picard.jar -h`
   
   ![](/Users/xinmengliao/Library/Application%20Support/marktext/images/2023-10-16-23-56-37-image.png)

#### 

#### BWA Installation

1. Get the software by cloning from GitHub
   
    `git clone https://github.com/lh3/bwa.git`

2. Go into the BWA directory  by `cd BWA`

3. Compile BWA by `make`

4. Test Installation `./bwa`

![](/Users/xinmengliao/Library/Application%20Support/marktext/images/2023-10-17-01-16-28-image.png)

5. Create alias for bwa

    `vim ~/.zshrc`

    `alias fastqc="<PATH>/bwa"`





#### FASTQC Installation

1. Download and unziq
   
   https://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.12.1.zip

2. Test installation by `./fastqc`

![](/Users/xinmengliao/Library/Application%20Support/marktext/images/2023-10-17-10-14-20-image.png)

3. Create alias for fastqc by 

    `vim ~/.zshrc`

    `alias fastqc="<PATH>/fastqc"`

4. Restart the Terminal 









******

SAMtools Installation (Option 2)

1. Download and unzip. [Releases · samtools/samtools · GitHub](https://github.com/samtools/samtools/releases/)[Releases · samtools/samtools · GitHub](https://github.com/samtools/samtools/releases/)

2. Go to the samtools directory by `cd path/smatools`

3. Install the configures by `./configure --prefix=<path>`
   
   `<path>`is the location to store configures, normally is `users/samtools`

4. `make`

5. `make install`

6. Test installation by `./samtools`

7. Add SAMtools into the environmental variable file by
   
   `echo "export PATH=$PATH:/path/samtools/bin/" >> ~/.bashrc`
   
   or
   
   `echo "export PATH=$PATH:/path/samtools/bin/" >> ~/.zshrc`

8. Or you can make alias for samtools by :
   
   `vim ~/.zshrc`
   
   `alias samtools="<PATH>/samtools"`