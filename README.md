# Track---generator
Курсовая работа
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.IO;
using System.Linq;
using System.Text;
using System.Windows.Forms;
using System.Threading;

namespace MediaPlayer
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }
                private List<PlayList> playList = new List<PlayList>();
                Random r = new Random(DateTime.Now.Millisecond);
        private void listBox1_SelectedIndexChanged(object sender, EventArgs e)
        {
            try { axWindowsMediaPlayer1.URL = playList[listBox1.SelectedIndex].Path; }
            catch { }
            axWindowsMediaPlayer1.PlayStateChange += new AxWMPLib._WMPOCXEvents_PlayStateChangeEventHandler(wplayer_Autoplay);
        }

        private void button1_Click(object sender, EventArgs e)
        {
            var sourceFileOpenFileDialog = new OpenFileDialog();
            sourceFileOpenFileDialog.Filter = "Media(*.*)|*.*";
            sourceFileOpenFileDialog.RestoreDirectory = true;
            sourceFileOpenFileDialog.Multiselect = true;
            sourceFileOpenFileDialog.Title = "Открыие медиа файлов";

            if (sourceFileOpenFileDialog.ShowDialog() == DialogResult.OK)
            {
                try
                {
                    foreach (string fileName in sourceFileOpenFileDialog.FileNames)
                    {
                        PlayList pl = new PlayList 
                        {
                            Name = Path.GetFileName(fileName),
                            Path = fileName
                        };

                        playList.Add(pl);
                        listBox1.Items.Add(pl.Name);
                    }
                }
                catch (Exception ex)
                {
                    MessageBox.Show(@"Error: Could not read file from disk. Original error: " + ex.Message);
                }
            }
        }

        private void button2_Click(object sender, EventArgs e)
        {
            var sourceFileOpenFileDialog = new OpenFileDialog
           {
               InitialDirectory = @"C:\",
               Filter = @"Playlist Files (*.pls)|*.pls",
               RestoreDirectory = true,
               Multiselect = false,
               Title = @"Please select playlist file for opening"
           };

            if (sourceFileOpenFileDialog.ShowDialog() == DialogResult.OK)
            {
                try
                {
                    string fileName = sourceFileOpenFileDialog.FileName;

                    using (var fs = new FileStream(fileName, FileMode.Open, FileAccess.Read))
                    {
                        using (var sr = new StreamReader(fs, Encoding.GetEncoding(1251)))
                        {
                            playList.Clear();
                            listBox1.Items.Clear();
                            while (!sr.EndOfStream)
                            {
                                string path = sr.ReadLine();
                                PlayList pl = new PlayList
                                {
                                    Name = Path.GetFileName(path),
                                    Path = path
                                };

                                playList.Add(pl);
                                listBox1.Items.Add(pl.Name);
                            }
                        }
                    }
                }
                catch (Exception ex)
                {
                    MessageBox.Show(@"Error: Could not read file from disk. Original error: " + ex.Message);
                }
            }
        }

        private void button3_Click(object sender, EventArgs e)
        {
            var sourceFileSaveFileDialog = new SaveFileDialog
            {
                InitialDirectory = @"C:\",
                Filter = @"Playlist Files (*.pls)|*.pls",
                RestoreDirectory = true,
                Title = @"Please select playlist file for saving"
            };

            if (sourceFileSaveFileDialog.ShowDialog() == DialogResult.OK)
            {
                try
                {
                    using (var fs = new FileStream(sourceFileSaveFileDialog.FileName, FileMode.Create, FileAccess.Write))
                    {
                        using (var sw = new StreamWriter(fs, Encoding.GetEncoding(1251)))
                        {
                            foreach (var item in playList)
                                sw.WriteLine(item.Path);
                        }
                    }
                }
                catch (Exception ex)
                {
                    MessageBox.Show(@"Error: Could not read file from disk. Original error: " + ex.Message);
                }
            }
        }

        private void axWindowsMediaPlayer1_Enter(object sender, EventArgs e)
        {
            
        }

        private void button4_Click(object sender, EventArgs e)
        {
            int k = listBox1.SelectedIndex;
            if (k + 1 > playList.Count -1)
            {
                k = 0;
                try { axWindowsMediaPlayer1.URL = playList[k].Path; }
                catch { }
                listBox1.SelectedIndex = k;
            }
            else
            {
                k = k + 1;
                try { axWindowsMediaPlayer1.URL = playList[k].Path; }
                catch { }
                listBox1.SelectedIndex = k;
            }
        }

        private void button5_Click(object sender, EventArgs e)
        {
            int k = listBox1.SelectedIndex;
            if (k - 1 < 0)
            {
                k = playList.Count -1;
                try { axWindowsMediaPlayer1.URL = playList[k].Path; }
                catch { }
                listBox1.SelectedIndex = k;
            }
            else
            {
                k = k - 1;
                try { axWindowsMediaPlayer1.URL = playList[k].Path; }
                catch { }
                listBox1.SelectedIndex = k;
            }
        }        
        private void button6_Click(object sender, EventArgs e)
        {
            int j = r.Next(playList.Count);
            listBox1.SelectedIndex = j;
            try { axWindowsMediaPlayer1.URL = playList[j].Path; }
            catch { }          
        }

        private void button7_Click(object sender, EventArgs e)
        {
            int k = listBox1.SelectedIndex;
            if (k + 1 > playList.Count - 1)
            {
                var temp = playList[0];
                playList[0] = playList[k];
                playList[k] = temp;

                var temp2 = listBox1.Items[0];
                listBox1.Items[0] = listBox1.Items[k];
                listBox1.Items[k] = temp2;
                listBox1.SelectedIndex = 0;
            }
            else
            {
                var temp = playList[k + 1];
                playList[k + 1] = playList[k];
                playList[k] = temp;

                var temp2 = listBox1.Items[k + 1];
                listBox1.Items[k + 1] = listBox1.Items[k];
                listBox1.Items[k] = temp2;
                listBox1.SelectedIndex = k + 1;
            }
        }

        private void button8_Click(object sender, EventArgs e)
        {
            int k = listBox1.SelectedIndex;
            if (k - 1 < 0)
            {
                var temp = playList[playList.Count - 1];
                playList[playList.Count - 1] = playList[k];
                playList[k] = temp;

                var temp2 = listBox1.Items[playList.Count - 1];
                listBox1.Items[playList.Count - 1] = listBox1.Items[k];
                listBox1.Items[k] = temp2;
                listBox1.SelectedIndex = playList.Count - 1;
            }
            else
            {
                var temp = playList[k - 1];
                playList[k - 1] = playList[k];
                playList[k] = temp;
                listBox1.SelectedIndex = k - 1;

                var temp2 = listBox1.Items[k - 1];
                listBox1.Items[k - 1] = listBox1.Items[k];
                listBox1.Items[k] = temp2;
            }
        }
        private void button9_Click(object sender, EventArgs e)
        {
            var k = listBox1.SelectedIndex; 
            int i = playList.Count - 1;
            while(i >= 0)
            {
                int j = r.Next(0,playList.Count - 1);
                    var temp = playList[i];
                    playList[i] = playList[j];
                    playList[j] = temp;

                    var temp2 = listBox1.Items[i];
                    listBox1.Items[i] = listBox1.Items[j];
                    listBox1.Items[j] = temp2;
                    i--;
                    listBox1.SelectedIndex = k;
            }
        }
        int p = 0;
        private void button10_Click(object sender, EventArgs e)
        {
               PlayList pl = new PlayList
                {
                    Name = playList[p].Name,
                    Path = playList[p].Path
                };
            playList.Add(pl);
            listBox1.Items.Add(pl.Name);
            p++;
        }

        private void button11_Click(object sender, EventArgs e)
        {
            int k = listBox1.SelectedIndex;
            int m = playList.Count;                       
            if (k + 1 < m)
            {
                try { axWindowsMediaPlayer1.URL = playList[k + 1].Path; }
                catch { }
                listBox1.SelectedIndex = k + 1;
            }
            else
            {
                try { axWindowsMediaPlayer1.URL = playList[k - 1].Path; }
                catch { }
                listBox1.SelectedIndex = k - 1;
            }
            listBox1.Items.RemoveAt(k);
            playList.RemoveAt(k);



        }
        void wplayer_Autoplay(object sender, AxWMPLib._WMPOCXEvents_PlayStateChangeEvent e)
            {
                int s = listBox1.SelectedIndex;
                int k = listBox1.SelectedIndex;
                if (axWindowsMediaPlayer1.playState == WMPLib.WMPPlayState.wmppsMediaEnded)
                {
                    axWindowsMediaPlayer1.close();
                    WMPLib.IWMPPlaylist pl = axWindowsMediaPlayer1.newPlaylist("myPlaylist", string.Empty);
                    while(s < playList.Count)
                    {                        
                        WMPLib.IWMPMedia temp = axWindowsMediaPlayer1.newMedia(playList[s].Path);  
                        pl.appendItem(temp);
                        s++;
                        if (k + 1 <= playList.Count - 1)
                        {
                        listBox1.SelectedIndex = k + 1;
                        }
                        else
                        {
                        listBox1.SelectedIndex = 0;
                        }
                    }
                    axWindowsMediaPlayer1.settings.autoStart = true;
                    axWindowsMediaPlayer1.currentPlaylist = pl;
                    axWindowsMediaPlayer1.Ctlcontrols.play();                    
                }
            }

        private void button12_Click(object sender, EventArgs e)
        {

        }
    }
}
