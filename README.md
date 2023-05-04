Download Link: https://assignmentchef.com/product/solved-csci4061-assignment-3-file-system
<br>
The goal of the project is to strengthen the understanding of ​<strong>File System </strong>​through a simplified implementation of the Unix file system.

Outline

With the confidence you have gained throughout your previous two projects, and the knowledge gained from your recent lecture notes and individual study, you are planning to take it to another level by saving the beautiful images you took during the winter holidays inside your own file system. However, after some more studies on the UNIX file system, you have kind of backed up from your initial ambitious plan and rather concentrating on making a miniature version of the file system.




To help you out, we came up with this assignment; we want you to code a simple, <strong><em>in-memory</em></strong>​ <strong><em>file system</em></strong> and similar to the previous assignments, you will work mainly with the image files. You have already converted all the images to jpeg files, and stored them in some directory, probably along with some other files. For this assignment, you will write a program that simulates an in-memory mini file system and some utility codes to help test the file system. You will be provided with a sample input directory, and some initial API’s to follow with.




From the input directory, ​you will copy (​<strong><em>write </em></strong>to the file system) <strong>all</strong>​<strong> of the files</strong> to your file system, provide a summary of the filesystem in a text file, and copy (​<strong>read</strong> from the file system) only the ​<strong><em>images (jpeg)</em></strong> to an output directory. Then convert the images to thumbnails inside the same directory and prepare an html file showing all of the thumbnail images.




<h1>File System Structure</h1>

<ol>

 <li>The file system will be in-memory and therefore, will exist only during the runtime of the code. Also, the filesystem will <strong>only store files</strong>​ , but not any directory.​</li>

 <li>The file system comprises of four components: Disk Blocks, Inode List, Directory Structure and Superblock. Each file inside the file system has a filename, inode number, and its corresponding inode list to point to the disk blocks where the content of the file is written. Have a graphical overview from ​ <strong><em>Figure 1</em></strong>​ <strong>. </strong></li>

 <li><strong>Disk blocks </strong>​are the in memory storage for data files. Total storage is divided into a fixed number of blocks. The number of blocks available are 8192 and each block is of 512 bytes.</li>

 <li><strong>Inode List</strong> stores, for each inode, the location of starting and ending blocks from the disk blocks, and some other information (see File Systems API). The number of inode is limited to 128.</li>

 <li><strong>Superblock</strong> holds the position of the next free inode and the next free disk block which</li>

</ol>

will help your to track the next place to write inside the Inode list, and the next disk block to use for storage.

<ol start="6">

 <li><strong>Directory structure</strong> maps the file names to their corresponding inode number. Maximum number of files that this directory structure can hold is ​<strong>128 </strong>​(as you have already guessed).</li>

 <li>For simplicity, every block is assigned to a file in its entirety. For example, if you have a file of size 510 bytes, you will assign 1 block (of size 512 bytes) to it, but If it exceeds 512 bytes (514 bytes for example), you have to assign 2 blocks (1024 bytes).</li>

</ol>










<strong><em>Figure</em></strong><em> <strong>1</strong>:</em> File System Structure with its ​<strong>four</strong> components: Directory Structure (left), Inode List (middle), Disk Blocks (right) and Superblock (top). All of the files inside the filesystem will have entries in ​<strong>Directory Structure</strong> (where filename and inode number of the file will be stored); detailed information of the file will be saved inside the ​<strong>Inode List</strong> along with the pointers to the real data blocks. ​<strong>Disk Blocks</strong> (​<strong><em>512 bytes</em></strong> each) will store real data for all of the files; the maximum number of disk block is ​<strong>8192</strong>.​ The track of next free entry inside Inode List and next free block will be kept through the ​<strong>Superblock</strong>.​ The file system can store at most 128 files, which also gives the maximum number for Inodes also.







<h1>Programming Guidelines</h1>

You are provided with an <strong>input</strong>​<strong> directory</strong>,​ where all of the image (jpeg) files (maybe along with some other files) reside. There may be directories inside the input directory, and you have to copy every files from there (unless there are duplicate files, in which case, you just need to copy one of those). To help with the coding, a ​<strong>File System API</strong> (mini_filesystem.h) is provided which will consist of file system structures and API for necessary file systems functions. To implement these APIs, you will also need to write some core file systems functions (​<strong>File System Calls</strong>)​ , which are only accessible from File System API’s and can’t be accessed from any outside functions.




<h2>File system API</h2>

We have provided the file system API (mini_filesystem.h) which has declarations for the four file system components and the File system interface (The actual implementation might be done in mini_filesystem.c). There are also declarations for the log_filename (char *) and Count (int) in this file, so that these are accessible both to the file system implementation and your test code (discussed later in the assignment).




<h2>The four Components</h2>

<ol>

 <li><strong>Superblock</strong>:​ a structure; next Inode and next Block should be integers.</li>

 <li><strong>Directory Structure</strong>​: an array of structures, where each structure holds:

  <ol>

   <li><strong>Filename</strong>​: assume the maximum length of filename as ​<strong>20</strong> (​<strong>including the extension</strong>​).</li>

   <li><strong>Inode Number</strong>​: array index of the corresponding inode in the Inode_List.</li>

  </ol></li>

 <li><strong>Inode List:</strong>​ an array of structures, where each of them includes:

  <ol>

   <li><strong>Inode number:</strong>​ Array index of this inode in the inode list</li>

   <li><strong>User id:</strong> This needs to be same as the user id of the original file​</li>

   <li><strong>Group id:</strong>​ This needs to be same as the group id of the original file</li>

   <li><strong>Size of the file in bytes:</strong> Indicates the current size of the file. Updated for every write command, set to 0 by create command</li>

   <li><strong>Start block:</strong> Array index of the first disk block that holds this file’s data​</li>

   <li><strong>End block:</strong>​ Array index of the last disk block that holds this file’s data</li>

   <li><strong>Flag:</strong>​ Set it to 1, when the file is open, and 0 when the file is closed.</li>

  </ol></li>

 <li><strong>Disk Blocks:</strong> an array (of size 8192) of character pointers. The actual blocks (of size 512 bytes) can be allocated and assigned to the entries in this array per necessity.</li>

</ol>




<h2>File system Interface</h2>

To expose the file system API to your test code, you should have following functions declared in this file (We have already included these into the mini_filesystem.h file):




int ​<strong>Initialize_Filesystem </strong>​(char* log_filename); int ​<strong>Create_File </strong>​(char* filename); int ​<strong>Open_File </strong>​(char* filename);

int ​<strong>Read_File </strong>​(int inode_number, int offset, int count, char* to_read);

int ​<strong>Write_File </strong>​(int inode_number, int offset, char* to_write); int ​<strong>Close_File </strong>​(int inode_number);

<h3>File system Implementation</h3>

The file system implementation (<strong>mini_filesystem.c</strong>​ )​ should provide the following two sets of functions:




<strong>File system Interface:</strong> These functions are declared as part of your API and can be called

from your main program. <strong>However,</strong>​<strong> these functions cannot access the file system structures directly</strong> (except for ​<strong>Initialize_Filesystem</strong>,​ as you need to initialize the file system structures inside this function). The functions<strong> must use the lower level file system calls described later in this assignment </strong>to change anything in the file system​ <strong>.</strong>​




<ol>

 <li><strong>Initialize_Filesystem: </strong>​Set the log file name as the filename provided as input. Set count to 0. Initialize (or allocate) anything else needed for your file system to work, for example, initial values for superblock. Return Success or Failure appropriately.</li>

</ol>




<ol start="2">

 <li><strong>Create_File: </strong>​Check whether the file you are going to create already exists in the directory structure. If yes, return with an appropriate error. If not, get the next free inode from the super block and create an entry for the file in the Inode_List. Then, using the returned inode number and filename, add the entry to the directory structure. Also, update the superblock since the next free inode index needs to be incremented. Then return appropriately.</li>

</ol>




<ol start="3">

 <li><strong>Open_File: </strong>​Search the directory for the provided file name. If found, set the inode flag for it to 1 and return the inode number. Otherwise, return appropriately.</li>

</ol>




<ol start="4">

 <li><strong>Read_File: </strong>For​ the given inode number, check whether the provided offset and number of bytes to be read is correct by comparing it with the size of the file. If correct, read the provided number of bytes and store them in the provided character array; return the number of bytes read. Otherwise, return an appropriate error. ​<strong>Hint:</strong> You may need to make multiple calls to ​<strong>block_read </strong>(​ See File System Calls) to implement this.</li>

</ol>




<ol start="5">

 <li><strong>Write_File: </strong>​For the given inode number, first check if the provided offset is correct by comparing it with the size of the file (​<strong>Note:</strong> a file is contiguous in this filesystem, you cannot write at any offset). If correct, write the provided string to the file blocks, update the ​<strong>inode</strong> (since this changes the ​<strong>file size</strong>,​ <strong>last</strong>​<strong> block</strong>) and <strong>superblock</strong>​ (as the <strong>next free disk block</strong> will change) with the right information and return the number of bytes written. If incorrect, return appropriately. ​<strong>Hint</strong>:​ You need to make multiple calls to <strong>block_write</strong>​ to implement this. You may do this in a loop:

  <ol>

   <li>Fetch the Superblock to get next free disk block number</li>

   <li>Write to this block by calling Block Write</li>

   <li>Update Inode</li>

   <li>Update the Superblock</li>

  </ol></li>

</ol>




<ol start="6">

 <li><strong>Close_File: </strong>​For the given inode number, set the inode flag to 0 if it is 1 and return appropriately.</li>

</ol>







<h2>File System Calls</h2>

This is a set of lower level functions that are not exposed to the user (or your test code) but these are the ones which can actually access and modify the file system structures. Use these to implement your file system API.




int ​<strong>Search_Directory</strong>​ (char* ​<strong>filename</strong>​)

<ul>

 <li>Search through the directory structure for the given filename, return Inode number of the file, if the file is found and error (-1) if it is not.</li>

</ul>




int ​<strong>Add_to_Directory</strong>​ (char* ​<strong>filename</strong>​, int ​<strong>inode_number</strong>​)

<ul>

 <li>Add an entry to the directory structure with the <strong>filename</strong>​ and <strong>inode_number</strong>​ provided as input. Return the ​<strong>status</strong> indicating whether it was able to add the entry to the directory successfully or not.</li>

</ul>




Inode ​<strong>Inode_Read</strong>​ (int ​<strong>inode_number</strong>​)

<ul>

 <li>For the given ​<strong>inode_number</strong>,​ if the file exists, return the ​<strong>Inode structure</strong> or ​<strong>-1</strong> if the file doesn’t exist.</li>

</ul>




int ​<strong>Inode_Write</strong>​ (int ​<strong>inode_number</strong>​, Inode <strong>input_inode</strong>​ ​)

<ul>

 <li>Copy the contents of Inode structure ​<strong>input_inode</strong> to the Inode present at the <strong>inode_number</strong>​.</li>

</ul>




int ​<strong>Block_Read</strong>​ (int block_number, int num_bytes , char* to_read )

<ul>

 <li>Read the given ​<strong>num_bytes</strong> from the ​<strong>block_number</strong> and write it to the provided character String ​<strong>to_read</strong>; return the number of bytes read.​</li>

</ul>




int ​<strong>Block_Write</strong>​ (int block_number, int num_bytes, char* to_write)

<ul>

 <li>Given the ​<strong>block_number</strong>​, write the contents of the string ​<strong>to_write</strong> to the block and return the number of bytes written.</li>

</ul>




Super_block ​<strong>Superblock_Read</strong>​ () ● Return the superblock structure.




int ​<strong>Superblock_Write</strong>​ (Super_block input_superblock)

<ul>

 <li>Copy the contents of ​<strong>input_superblock</strong> to the superblock structure.​</li>

</ul>




<h3>Testing the File System</h3>

Now that the file system implementation is done, the time for testing has come at last. Before writing your test code, we want you to make <strong>a</strong>​<strong> library file for the API and system calls</strong> (See at the end of the document for details of library creation). From the test program, you will access the necessary File System API through the library; to assist you on writing your test program, we are providing a dummy of the source code (test.c), and four interfaces to fill in:




void ​<strong>write_into_filesystem</strong>​(char* input_directory, char *log_filename)

<ul>

 <li>This function will read all of the files inside the input_directory​ (which might be nested). Also it will provide the ​log_filename;​ check if the file exists, and if it does, remove and create a new one. Your program should first initialize the file system by making a call to Initialize_Filesystem by passing log_filename​ ​ to it.</li>

</ul>




void ​<strong>make_filesystem_summary</strong>​(char* filename)

<ul>

 <li>This function will make a summary of the filesystem and write into a file named filename​. The format of the output file is flexible but it should include: ​<strong>Filename</strong>​, <strong>extension</strong>​, ​<strong>Inode No</strong>, and ​ <strong>Size</strong>​​.</li>

</ul>




void ​<strong>read_images_from_filesystem_and_write_to_output_directory</strong>​(char* output_directory)

<ul>

 <li>This function will read all the image (jpg) files from the filesystem and write into the output_directory​.</li>

</ul>




void ​<strong>generate_html_file</strong>​(char* filename)

<ul>

 <li>This function will convert the image files inside output directory to thumbnail images, write into the same directory and prepare a html file using the thumbnails.</li>

</ul>




After the test code is written, compile it using the library and make an executable. The executable can be used like: ​&lt;executable-name&gt; &lt;input_dir&gt; &lt;output_dir&gt; &lt;log_filename&gt; (​ for example, ./test input_dir output_dir log.txt​            ).​




<h3>Generic points</h3>

<ol>

 <li>For each file created, there should be a corresponding entry in the directory structure and an inode in the inode list.</li>

 <li>The files are to be laid out contiguously on the disk, one after the other and the inode should hold the start and end block number indicating the first file block and last file block.</li>

 <li>You should log an entry to the log file for every access made to any of the file structure. See the ​<strong>logging</strong>​ section below for more details.</li>

 <li>Your file system should keep track of the number of accesses made to it. At the end of your test program, you should print this Count.</li>

 <li>You should increment the count variable for every access made to any of the file structure. This can be easily achieved by incrementing the count every time a call is made to the low level function.</li>

 <li>Status and return values can follow the following pattern:

  <ol>

   <li>-1 or NULL =&gt; Error</li>

   <li>0 or the value requested =&gt; Success</li>

  </ol></li>

</ol>




<strong>Test Data:</strong> A​ compressed tar file is provided with this Assignment. Note that your input directory can be nested.




<strong>Logging: </strong>​For every access done to the mini file system, be it superblock, directory structure, inode and disk block (an entry for every disk block access, and not every file access), add an entry to the log file. You can accomplish this by adding entries from the lower level file system calls implemented by you. Each entry in the log file should have the following format:

&lt;Timestamp&gt; &lt;File structure Accessed&gt; &lt;Read/Write&gt;

Time Stamp may be in any human readable format but has to include microseconds (you can use ​<strong>hr:min:sec:micro-secs</strong> if you can’t decide).​




<h3>Output Directory and HTML file</h3>

After you have copied the images from your mini file system to the output directory, follow the steps below to create the HTML file:




<ol>

 <li>Create thumbnail image for each of the image in this output directory. You can save them in the output directory itself. The name of the thumbnail image should be &lt;current_filename&gt;_thumb.jpg Note that thumbnail images must all be 200 pixels in the largest dimension (either width or height)</li>

 <li>Using the contents of output directory (Images and thumbnails), create an HTML file with the name ​<strong>html</strong> (below is an example file)​</li>

</ol>




&lt;html&gt;

&lt;head&gt;

&lt;title&gt;Image 01&lt;/title&gt;

&lt;/head&gt;

&lt;body&gt;

&lt;a href=”images/sunset.jpg”&gt;

&lt;img src=”images/thumbs/sunset_thumb.jpg”/&gt;&lt;/a&gt; &lt;/body&gt;

&lt;/html&gt;




<h3>Experiment</h3>

Once you have the file system setup and all the files written to the mini file system, you will be performing read operation on the files from your test program. To get the sense of approximately how much impact a defragmentation operation can create, you will use your program to count the number of accesses made to the mini file system. This can be easily accounted for using the<strong> “Count” </strong>​Variable and by incrementing it once in every low level function. This count accounts for ​<strong>both read and write</strong> operation. Half it (to account approximately for read operation) and then take average over total number of files. This should give you the ​<strong>average number of access made per file read</strong>​. With the value calculated for average number of access made to the file system per file read, calculate the best case and average case time required for reading a file.




<strong>Best case</strong>​ – When the file system is appropriately and contiguously laid.

<strong>Average case</strong> – When the file system is fragmented and the blocks are mapped randomly to the disk sectors.




Use the following disk parameters for this calculation:

<ul>

 <li>Rotation Rate – 15,000 RPM</li>

 <li>Average Seek time – 4 ms</li>

 <li>Average number of sectors per track – 1000 ● Sector size = Block size = 512 bytes.</li>

</ul>




Assume that the time to position the head over the first block is Average Seek Time + Average Rotation time.




<h3>Error Handling</h3>

<ul>

 <li>Errors must be handled gracefully, with informative error messages on standard error.</li>

 <li>You cannot count on your user always giving you the right number of arguments. If the wrong number of arguments is given, then your program should print a usage message and exit with a status of 1.</li>

 <li>You must also check for errors on the system calls you use, and use the <strong>perror</strong>​ ()​ function to report them. Function calls will be needed for getting the file related data while populating the inodes.</li>

 <li>You may add your own error reporting if you feel it was necessary.</li>

</ul>




<h3>Platform</h3>

You may work on the platform of your choice: Linux, Solaris, MacOSX or Windows. However, you need to ensure that your script works correctly on one of the CSELabs UNIX machines, where we will grade your assignments.




<strong>Teamwork</strong>

You may do this assignment individually or with one partner.




<h3>Deliverables</h3>

See the course syllabus for general requirements and assignment page for submission guideline. The specific requirements of this assignment are as following:




<ol>

 <li>txt should include

  <ol>

   <li>Personal information for the group</li>

   <li>CSELab machine you tested your code on</li>

   <li>Syntax and usage pointers for your program</li>

   <li>Any special test cases or anomalies handled/not handled by your program.</li>

   <li>Experiment results</li>

  </ol></li>

 <li>Source code and library files. All *.h, *.c files required to execute your program. Also add the static library file and a Makefile to create an executable using the library and test program.</li>

 <li>Your commentary as described in the syllabus. This should explain, briefly, what your code does, how it works, how you use it, and how to interpret its output. It should be no longer than TWO pages in length.</li>

</ol>


