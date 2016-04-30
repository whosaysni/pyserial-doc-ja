=============
pySerial
=============


.. note:: *** 重要 ***
  このドキュメントは pySerial 配布に含まれる README.txt を日本語訳
  したものです．オリジナルのドキュメントは free software license の
  ライセンスの下で配布されています．詳細は末尾の「ライセンス」を
  読んで下さい．

.. warning:: 注意
  このドキュメントは 2000 年ごろのもので、現在の pySerial に合っていな
  いかもしれません。
  最新版は https://pyserial.readthedocs.io/en/latest/
  を確認してください。

このモジュールはシリアルポートへのアクセスをカプセル化します．
バックエンドとしては，Windows，Liunx，BSD （そしておそらく POSIX 互換
のシステム）で動く標準的な Python と，Jython をサポートします．
"serial" と名づけられたモジュールが自動的に正しいバックエンドを選択
します．

このソフトウェアは free software license の下で配布されます．
詳細については LICENSE.txt を読んでください．

プロジェクトのホームページ: pyserial.sourceforge.net
(C) 2001-2003 Chris Liechti <cliechti@gmx.net>


主な機能
---------

- サポートされている全てのプラットフォームで，クラスに基づいた同じ
  インタフェースを提供しています．
- ポートはゼロから順に番号付けして識別されるため，プラットフォーム依存の
  ポート名を知る必要がありません．
- 番号によるポートへのアクセスがうまくいかない場合にはポート名を指定する
  ことができます．
- 異なるバイトサイズ，ストップビット，パリティ，RTS/CTS および xon/xoff
  フローコントロールをサポートしています．
- 受信タイムアウト有り／無し，ブロック，非ブロックのどちらでも動作します．
- "read" および "write" によるファイル類似の API です ("readline" 等も
  サポートしています)．
- このパッケージに含まれているファイルは 100% pure Python です．
  Windows や Jython 上では，ファイルは非標準だが広く使われているパッケージ
  (それぞれ win32all および JavaComm) に依存しています．
  POSIX (Linux, BSD) は標準の Python ディストリビューション上のモジュール
  を使います．
- ポートはバイナリ転送でセットアップされます．バイトストリップや CR-LF 
  文字の変換等（これらは POSIX ではよく有効にされています）は行いません．
  このため，このモジュールは広範に利用できるようになっています．

要求仕様
--------
- Python 2.2 以降
- Windowsでは win32all 拡張ライブラリが必要です．
- Java/Jython では "Java Communications" (JavaComm) 拡張ライブラリが必要です．

インストール
------------
ファイルをアーカイブから展開して，シェルまたはコマンドコンソールで展開
ディレクトリに移り，"python setup.py install" を実行すれば，残りの作業
は Distutils がやってくれます．

ファイルは "Lib/site-packages" ディレクトリにインストールされます．

USB アダプタ接続のシリアルポート
------------------------
これらのアダプタは Mac OSX および Windows で動作すると報告されています．
Windows ではアダプタが通常の COM ポートにマップされますが，Mac OSX や
その他のプラットフォームでは特殊なデバイス名を持っています．

Mac OSX: "/dev/[cu|tty].USA<adaptername><USB-part>P<serial-port>.1"
    例えば "/dev/cu.USA19QW11P1.1"

Linux: "/dev/usb/ttyUSB[n]" or "/dev/ttyUSB[n]"
    first for for RedHat, second form for Debian.
    例えば "/dev/usb/ttyUSB0"

シリアルポート名として上記のようなデバイス名を用いるか，
"ln -s /dev/cu.USA19QW11P1.1 /dev/cuaa0" または "ln -s /dev/usb/ttyUSB0
/dev/ttyS4" などとして，一般的なデバイス名へのリンクを作成してください．

簡単な解説
------------------
ポート 0 を 9600, 8, N,1, no timeout で開く
>>> import serial
>>> ser = serial.Serial(0)  # 最初に見つかったポートを開く
>>> print ser.portstr       # どのポートが実際に使われているか調べる
>>> ser.write("hello")      # 文字列を書き込む
>>> ser.close()             # ポートを閉じる．

名前のついたポートを 19200,8,N,1, 1s timeout で開く
>>> ser = serial.Serial('/dev/ttyS1', 19200, timeout=1)
>>> x = ser.read()          #	1 バイト読み込む
>>> s = ser.read(10)        # 10 バイト読み込む (タイムアウト付き)．
>>> line = ser.readline()   # \n で終端された文字列を1行読む
>>> ser.close()

2番目に見つかった 38400,8,E,1, non blocking HW handshaking で開く
>>> ser = serial.Serial(1, 38400, timeout=0,
...                     parity=serial.PARITY_EVEN, rtscts=1)
>>> s = ser.read(100)       # 上限 100 バイトまで読み出す
...                         # またはバッファ上にあるだけデータを読む

Serial インスタンスを取得して，その後に設定し開く
>>> ser = serial.Serial()
>>> ser.baudrate = 19200
>>> ser.port = 0
>>> ser
Serial<id=0xa81c10, open=False>(port='COM1', baudrate=19200, bytesize=8,
parity='N', stopbits=1, timeout=None, xonxoff=0, rtscts=0)
>>> ser.open()
>>> ser.isOpen()
True
>>> ser.close()
>>> ser.isOpen()
False


"readline" を使うときには注意してください．ポートを開くときには
タイムアウトを必ず指定するようにしてください．さもないと新しい
改行文字を受信しないかぎり永遠にブロックすることになります．
また，"readlines" はタイムアウトが設定されているときのみ動作
するので注意してください．"readlines" はタイムアウトが設定されて
いることに依存していて，タイムアウトを EOF （ファイル終端）と
みなします．ポートが正しく開かれていない場合，例外が発行されます．


Serial クラスへのパラメタ
-------------------------
ser = serial.Serial(
    port,                   # デバイス番号．番号はゼロから開始．
                            # どうやってもうまくいかない場合，ユーザは
                            # デバイスを文字列で指定できるが，
                            # 移植性は失われる．
    baudrate=9600,          # baud レート
    bytesize=EIGHTBITS,     # データビット数
    parity=PARITY_NONE,     # パリティチェックを有効にするかどうか
    stopbits=STOPBITS_ONE,  # ストップビット数
    timeout=None,           # タイムアウト値の指定．None は永遠にブロック
    xonxoff=0,              # ソフトウェアフロー制御を有効にする
    rtscts=0,               # RTS/CTS フロー制御を有効にする
)

ポートはオブジェクト生成時にすぐ開かれます．オプションは以下のとおりです:
timeout=None            # 永遠にブロックする
timeout=0               # 非ブロックモード (read() 呼び出しが即座に返る)
timeout=x               # タイムアウトを x 秒 (float で指定できる) に設定

Serial オブジェクトのメソッド
-----------------------------
close()                 # すぐにポートを閉じる
setBaudrate(baudrate)   # ポートを開く際の baud レートを変える
inWaiting()             # 受信バッファ中の文字数を返す
read(size=1)            # "size" 文字の文字列を読み出す
write(s)                # ポートに文字列を書き出し
flushInput()            # 入力バッファをフラッシュ
flushOutput()           # 出力バッファをフラッシュ
sendBreak()             # break を送信
setRTS(level=1)         # RTS 信号線を指定した論理レベルに設定
setDTR(level=1)         # DTR 信号線を指定した論理レベルに設定
getCTS()                # CTS 信号線の状態を返す
getDSR()                # DSR 信号線の状態を返す
getRI()                 # RI 信号線の状態を返す
getCD()                 # CD 信号線の状態を返す

Serial インスタンスの属性
-------------------------
読み出し専用:
portstr                 # デバイス名
BAUDRATES               # 有効なボーレート設定のリスト
BYTESIZES               # 有効なデータビット数設定のリスト
PARITIES                # 有効なパリティ設定のリスト
STOPBITS                # 有効なストップビット数設定のリスト

以下の属性には新たに値を代入することができます．ポートは再設定
されます．これはその際にポートがすでに開かれていても行われます:
port                    # ユーザが設定したポート名/番号
baudrate                # 現在のボーレート設定
bytesize                # データビット数
parity                  # パリティ設定
stopbits                # ストップビット (1 または 2 で指定)
timeout                 # タイムアウト設定
xonxoff                 # XonXoff フロー制御が有効かどうか
rtscts                  # ハードウェアフロー制御が有効かどうか

定数
----
パリティ:
    serial.PARITY_NONE
    serial.PARITY_EVEN
    serial.PARITY_ODD
ストップビット:
    serial.STOPBITS_ONE
    serial.STOPBITS_TWO
バイトサイズ:
    serial.FIVEBITS
    serial.SIXBITS
    serial.SEVENBITS
    serial.EIGHTBITS

小技と豆知識 (Tips & Tricks)
----------------------------
- プロトコルによっては，行終端文字に LF ("\n") だけでなく CR LF ("\r\n")
  を必要とします．AT コマンドセットを持つ電話モデムがその例です．

- 利用可能なシリアルポートの検索は，プラットフォームによって
  より多くの，またはより少ない結果を返します．Roger Binns によるツール
  http://cvs.sourceforge.net/cgi-bin/viewcvs.cgi/bitpim/comscan/
  を参照してください．

参考文献
--------
- Python: http://www.python.org
- Jython: http://www.jython.org
- win32all: http://starship.python.net/crew/mhammond/
  and http://www.activestate.com/Products/ActivePython/win32all.html
- Java@IBM http://www-106.ibm.com/developerworks/java/jdk/
  (JavaComm links are on the download page for the respective platform jdk)
- Java@SUN http://java.sun.com/products/

LICENSE
-------
Copyright (c) 2001 Chris Liechti <cliechti@gmx.net>;
All Rights Reserved.

This is the Python license. In short, you can use this product in
commercial and non-commercial applications, modify it, redistribute it.
A notification to the author when you use and/or modify it is welcome.


TERMS AND CONDITIONS FOR ACCESSING OR OTHERWISE USING THIS SOFTWARE
===================================================================

LICENSE AGREEMENT
-----------------

1. This LICENSE AGREEMENT is between the copyright holder of this
product, and the Individual or Organization ("Licensee") accessing
and otherwise using this product in source or binary form and its
associated documentation.

2. Subject to the terms and conditions of this License Agreement,
the copyright holder hereby grants Licensee a nonexclusive,
royalty-free, world-wide license to reproduce, analyze, test,
perform and/or display publicly, prepare derivative works, distribute,
and otherwise use this product alone or in any derivative version,
provided, however, that copyright holders License Agreement and
copyright holders notice of copyright are retained in this product
alone or in any derivative version prepared by Licensee.

3. In the event Licensee prepares a derivative work that is based on
or incorporates this product or any part thereof, and wants to make
the derivative work available to others as provided herein, then
Licensee hereby agrees to include in any such work a brief summary of
the changes made to this product.

4. The copyright holder is making this product available to Licensee on
an "AS IS" basis. THE COPYRIGHT HOLDER MAKES NO REPRESENTATIONS OR
WARRANTIES, EXPRESS OR IMPLIED.  BY WAY OF EXAMPLE, BUT NOT LIMITATION,
THE COPYRIGHT HOLDER MAKES NO AND DISCLAIMS ANY REPRESENTATION OR
WARRANTY OF MERCHANTABILITY OR FITNESS FOR ANY PARTICULAR PURPOSE OR
THAT THE USE OF THIS PRODUCT WILL NOT INFRINGE ANY THIRD PARTY RIGHTS.

5. THE COPYRIGHT HOLDER SHALL NOT BE LIABLE TO LICENSEE OR ANY OTHER
USERS OF THIS PRODUCT FOR ANY INCIDENTAL, SPECIAL, OR CONSEQUENTIAL
DAMAGES OR LOSS AS A RESULT OF MODIFYING, DISTRIBUTING, OR OTHERWISE
USING THIS PRODUCT, OR ANY DERIVATIVE THEREOF, EVEN IF ADVISED OF THE
POSSIBILITY THEREOF.

6. This License Agreement will automatically terminate upon a material
breach of its terms and conditions.

7. Nothing in this License Agreement shall be deemed to create any
relationship of agency, partnership, or joint venture between the
copyright holder and Licensee. This License Agreement does not grant
permission to use trademarks or trade names from the copyright holder
in a trademark sense to endorse or promote products or services of
Licensee, or any third party.

8. By copying, installing or otherwise using this product, Licensee
agrees to be bound by the terms and conditions of this License
Agreement.
