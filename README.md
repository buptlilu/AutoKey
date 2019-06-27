# AutoKey
c#自动按键参考资料

using System;
using System.Drawing;
using System.Runtime.InteropServices;
using System.Windows.Forms;

namespace SoftKeyBoard {
    public partial class Form1 : Form {

        #region 把当前窗体设置为浮动工具条。不会影响其它进程的窗体的光标焦点。虽然这个窗体现在为当前激活的前台窗体，但光标仍然停在其他进程的窗体上
        private const int WS_EX_TOOLWINDOW = 0x00000080;
        private const int WS_EX_NOACTIVATE = 0x08000000;
        //重写该方法实现窗体变为浮动工具条，不获取光标焦点
        protected override CreateParams CreateParams {
            get {
                CreateParams cp = base.CreateParams;
                cp.ExStyle |= (WS_EX_NOACTIVATE | WS_EX_TOOLWINDOW);
                cp.Parent = IntPtr.Zero;
                return cp;
            }
        }
        #endregion

        public Form1() {
            this.TopMost = true;
            InitializeComponent();
        }

        //模拟键盘API  键值用byte最准确
        [DllImport("user32.dll", EntryPoint = "keybd_event", SetLastError = true)]
        public static extern void keybd_event1(byte bVk, byte bScan, uint dwFlags, uint dwExtraInfo);
        #region 系统全局 大小写按键
        bool isCapitalization = false; //是否大写，默认小写
        private void 大小写_Click(object sender, EventArgs e) {
            const int KEYEVENTF_EXTENDEDKEY = 0x1;
            const int KEYEVENTF_KEYUP = 0x2;
            keybd_event1(0x14, 0x45, KEYEVENTF_EXTENDEDKEY, 0);
            keybd_event1(0x14, 0x45, KEYEVENTF_EXTENDEDKEY | KEYEVENTF_KEYUP, 0);
            if (!isCapitalization) {
                this.label1.Text = "目前：大写";
                isCapitalization = true;
                this.大小写.BackColor = Color.Blue;
            }
            else {
                this.label1.Text = "目前：小写";
                isCapitalization = false;
                this.大小写.BackColor = Color.White;
            }
        }


        #endregion

        //模拟键盘API  和上面的API参数类型有区别   键值用Keys，大部分准确
        [DllImport("user32.dll", EntryPoint = "keybd_event", SetLastError = true)]
        public static extern void keybd_event(Keys bVk, byte bScan, uint dwFlags, uint dwExtraInfo);
        #region 系统全局 windows键
        private void Windows_Click(object sender, EventArgs e) {
            keybd_event(Keys.LWin, 0, 0, 0);
            keybd_event(Keys.LWin, 0, 2, 0);

            //注意点：一定要按下，再释放，有些功能才会有果（ctrl   shift   等）。 0代表按下，2代表释放
            //下面2行代码同样可以做到 系统全局-大小写
            //keybd_event(Keys.CapsLock,0,0,0);
            //keybd_event(Keys.CapsLock, 0, 2, 0);
        }
        #endregion

        #region Ctrl 是否按下
        bool isCtrl = false;
        private void CtrlClick(object sender, EventArgs e) {
            if (!isCtrl) {
                isCtrl = true;
                this.label2.Text = "ctrl已按下";
                this.button80.BackColor = Color.Blue;
            }
            else {
                isCtrl = false;
                this.label2.Text = "ctrl未按下";
                this.button80.BackColor = Color.White;
            }
        }
        #endregion
        
        #region 数字键   多个虚拟数字键按钮-统一监听
        private void NUmberClick(object sender, EventArgs e) {
            int number = Convert.ToInt32(((Button)sender).Text); //0 1 2 3 4 5 6 7 8 9
            switch (number) {
                case 0:
                    keybd_event(Keys.D0, 0, 0, 0);
                    break;
                case 1:
                    keybd_event(Keys.D1, 0, 0, 0);
                    break;
                case 2:
                    keybd_event(Keys.D2, 0, 0, 0);
                    break;
                case 3:
                    keybd_event(Keys.D3, 0, 0, 0);
                    break;
                case 4:
                    keybd_event(Keys.D4, 0, 0, 0);
                    break;
                case 5:
                    keybd_event(Keys.D5, 0, 0, 0);
                    break;
                case 6:
                    keybd_event(Keys.D6, 0, 0, 0);
                    break;
                case 7:
                    keybd_event(Keys.D7, 0, 0, 0);
                    break;
                case 8:
                    keybd_event(Keys.D8, 0, 0, 0);
                    break;
                case 9:
                    keybd_event(Keys.D9, 0, 0, 0);
                    break;
            }
        }
        #endregion

        #region 字母键，组合键   多个虚拟字母键按钮-统一监听
        private void CharClick(object sender, EventArgs e) {
            String cr = ((Button)sender).Text;
            switch (cr) {
                case "a":
                    if (isCtrl) { //组合键 ctrl + a   示例：其它组合键，自行摸索
                        keybd_event(Keys.ControlKey, 0, 0, 0);
                        keybd_event(Keys.A, 0, 0, 0);
                        keybd_event(Keys.ControlKey, 0, 2, 0);
                        keybd_event(Keys.A, 0, 2, 0);
                    }
                    else {
                        keybd_event(Keys.A, 0, 0, 0);
                    }
                    break;
                case "b":
                    keybd_event(Keys.B, 0, 0, 0);
                    break;
                case "c":
                    keybd_event(Keys.C, 0, 0, 0);
                    break;
                case "d":
                    keybd_event(Keys.D, 0, 0, 0);
                    break;
                case "e":
                    keybd_event(Keys.E, 0, 0, 0);
                    break;
                case "f":
                    keybd_event(Keys.F, 0, 0, 0);
                    break;
                case "g":
                    keybd_event(Keys.G, 0, 0, 0);
                    break;
                case "h":
                    keybd_event(Keys.H, 0, 0, 0);
                    break;
                case "i":
                    keybd_event(Keys.I, 0, 0, 0);
                    break;
                case "j":
                    keybd_event(Keys.J, 0, 0, 0);
                    break;
                case "k":
                    keybd_event(Keys.K, 0, 0, 0);
                    break;
                case "l":
                    keybd_event(Keys.L, 0, 0, 0);
                    break;
                case "m":
                    keybd_event(Keys.M, 0, 0, 0);
                    break;
                case "n":
                    keybd_event(Keys.N, 0, 0, 0);
                    break;
                case "o":
                    keybd_event(Keys.O, 0, 0, 0);
                    break;
                case "p":
                    keybd_event(Keys.P, 0, 0, 0);
                    break;
                case "q":
                    keybd_event(Keys.Q, 0, 0, 0);
                    break;
                case "r":
                    keybd_event(Keys.R, 0, 0, 0);
                    break;
                case "s":
                    keybd_event(Keys.S, 0, 0, 0);
                    break;
                case "t":
                    keybd_event(Keys.T, 0, 0, 0);
                    break;
                case "u":
                    keybd_event(Keys.U, 0, 0, 0);
                    break;
                case "v":
                    keybd_event(Keys.V, 0, 0, 0);
                    break;
                case "w":
                    keybd_event(Keys.W, 0, 0, 0);
                    break;
                case "x":
                    keybd_event(Keys.X, 0, 0, 0);
                    break;
                case "y":
                    keybd_event(Keys.Y, 0, 0, 0);
                    break;
                case "z":
                    keybd_event(Keys.Z, 0, 0, 0);
                    break;
             }
        }
        #endregion

        #region F1-F12  虚拟键统一监听
        private void FnClick(object sender, EventArgs e) {
            String fn = ((Button)sender).Text;
            switch (fn) {
                case "F1":
                    keybd_event(Keys.F1, 0, 0, 0);
                    break;
                case "F2":
                    keybd_event(Keys.F2, 0, 0, 0);
                    break;
                case "F3":
                    keybd_event(Keys.F3, 0, 0, 0);
                    break;
                case "F4":
                    keybd_event(Keys.F4, 0, 0, 0);
                    break;
                case "F5":
                    keybd_event(Keys.F5, 0, 0, 0);
                    break;
                case "F6":
                    keybd_event(Keys.F6, 0, 0, 0);
                    break;
                case "F7":
                    keybd_event(Keys.F7, 0, 0, 0);
                    break;
                case "F8":
                    keybd_event(Keys.F8, 0, 0, 0);
                    break;
                case "F9":
                    keybd_event(Keys.F9, 0, 0, 0);
                    break;
                case "F10":
                    keybd_event(Keys.F10, 0, 0, 0);
                    break;
                case "F11":
                    keybd_event(Keys.F11, 0, 0, 0);
                    break;
                case "F12":
                    keybd_event(Keys.F12, 0, 0, 0);
                    break;
            }
        }
        #endregion

        #region 右边符号键   统一监听 未完成全部代码
        private void LeftOtherClick(object sender, EventArgs e) {
            String otherChar = ((Button)sender).Text;
            switch (otherChar) {
                case "BackSpace":
                    keybd_event(Keys.Back, 0, 0, 0);
                    break;
                case "Enter":
                    keybd_event(Keys.Enter, 0, 0, 0);
                    break;
                case " ":
                    keybd_event(Keys.Space, 0, 0, 0);
                    break;
                //case "#":
                //    break;
                //case "$":
                //    break;
                //case "%":
                //    break;
                //case "^":
                //    break;
                //case "&&":
                //    break;
                //case "*":
                //    break;
                //case "(":
                //    break;
                //case ")":
                //    break;
                //case "":
                //    break;
                //case "":
                //    break;
                //todo   other

            }
        }
        #endregion

        //除了调用 user32.dll 实现模拟按键外，还有另外一种 SendKeys
        //组合键：Ctrl = ^    Shift = +    Alt = %
        private void button1_Click(object sender, EventArgs e) {
            //括号中的内容就是虚拟按键的内容，可以有如下值：
            //ENTER  BACKSPACE  END  HOME  DELETE   +   -   *   /   \\     等...
            SendKeys.Send("^+");  //ctrl + shift     由于用上面的user32.dll 我没解决 ctrl +shift组合键的问题，所以这里可以解决
            //SendKeys.Send("a"); //a  区分大小写
            //SendKeys.Send("^{A}");  //ctrl + A

        }

    }
}
--------------------- 
作者：小黑刘颜 
来源：CSDN 
原文：https://blog.csdn.net/qq_38261174/article/details/85316708 
版权声明：本文为博主原创文章，转载请附上博文链接！
