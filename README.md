# To-do-list-using-pyhton

from PyQt5 import QtCore, QtGui, QtWidgets

from PyQt5.QtWidgets import QMessageBox

import sqlite3
# Create a datbase or connect with it
conn = sqlite3.connect('mylist.db')
# create a cursor
cur = conn.cursor()
# Create a table
cur.execute(""" CREATE TABLE if not exists todo_list(
    list_item text
) """)

#commit the changes
conn.commit()

#Close the connection
conn.close()

class Ui_MainWindow(object):
    def setupUi(self, MainWindow):
        MainWindow.setObjectName("MainWindow")
        MainWindow.resize(372, 439)
        self.centralwidget = QtWidgets.QWidget(MainWindow)
        self.centralwidget.setObjectName("centralwidget")
        self.lineEdit = QtWidgets.QLineEdit(self.centralwidget)
        self.lineEdit.setGeometry(QtCore.QRect(20, 30, 331, 31))
        self.lineEdit.setObjectName("lineEdit")
        self.add_pushButton = QtWidgets.QPushButton(self.centralwidget, clicked = lambda: self.add_it())
        self.add_pushButton.setGeometry(QtCore.QRect(20, 70, 93, 31))
        self.add_pushButton.setObjectName("add_pushButton")
        self.delete_pushButton_2 = QtWidgets.QPushButton(self.centralwidget, clicked = lambda: self.delete_it())
        self.delete_pushButton_2.setGeometry(QtCore.QRect(140, 70, 93, 31))
        self.delete_pushButton_2.setObjectName("delete_pushButton_2")
        self.clearlist_pushButton_3 = QtWidgets.QPushButton(self.centralwidget, clicked = lambda: self.clear_it())
        self.clearlist_pushButton_3.setGeometry(QtCore.QRect(260, 70, 93, 31))
        self.clearlist_pushButton_3.setObjectName("clearlist_pushButton_3")
        self.listWidget = QtWidgets.QListWidget(self.centralwidget)
        self.listWidget.setGeometry(QtCore.QRect(20, 151, 331, 221))
        self.listWidget.setObjectName("listWidget")
        self.save_pushButton = QtWidgets.QPushButton(self.centralwidget, clicked = lambda: self.save_it())
        self.save_pushButton.setGeometry(QtCore.QRect(20, 110, 331, 31))
        font = QtGui.QFont()
        font.setPointSize(10)
        font.setBold(True)
        font.setWeight(75)
        self.save_pushButton.setFont(font)
        self.save_pushButton.setObjectName("save_pushButton")
        MainWindow.setCentralWidget(self.centralwidget)
        self.menubar = QtWidgets.QMenuBar(MainWindow)
        self.menubar.setGeometry(QtCore.QRect(0, 0, 372, 26))
        self.menubar.setObjectName("menubar")
        MainWindow.setMenuBar(self.menubar)
        self.statusbar = QtWidgets.QStatusBar(MainWindow)
        self.statusbar.setObjectName("statusbar")
        MainWindow.setStatusBar(self.statusbar)

        self.retranslateUi(MainWindow)
        QtCore.QMetaObject.connectSlotsByName(MainWindow)

        # get all item from the database
        self.grab_all()
    
    def grab_all(self):
        conn = sqlite3.connect('mylist.db')
        # create a cursor
        cur = conn.cursor()
        
        cur.execute("SELECT * FROM todo_list")
        records = cur.fetchall()





        #commit the changes
        conn.commit()

        #Close the connection
        conn.close()

        # loop through records and add to screen 
        for record in records:
            self.listWidget.addItem(str(record[0]))


        # Add items To the list:
    def add_it(self):
        # take item from the list box
        item = self.lineEdit.text()

        # Add item to the list
        self.listWidget.addItem(item)

        # Clear the item box
        self.lineEdit.setText("")


    # Add items To the list:
    def delete_it(self):
        clicked = self.listWidget.currentRow()
        # self.lineEdit.setText(str(clicked))

        #delete selected row
        self.listWidget.takeItem(clicked)
        
    # Add items To the list:
    def clear_it(self):
        self.listWidget.clear()

    # Save to the database
    def save_it(self):
        conn = sqlite3.connect('mylist.db')
        # create a cursor
        cur = conn.cursor()
        
        # Delete everythig in the database table stored previously
        cur.execute('DELETE FROM todo_list;',)



        
        # Create list to hold items
        items = []
        # loop through the listwidget and pull out each entry
        for index in range(self.listWidget.count()):
            items.append(self.listWidget.item(index))

        for item in items:
            # print(item.text())
            # add item to the table
            cur.execute("INSERT INTO todo_list VALUES(:item)",
                {
                    'item':item.text(),
                }
            
            )

        # commit the changes
        conn.commit()

        # Close the connection
        conn.close()

        # Pop up box
        msg = QMessageBox()
        msg.setWindowTitle("Saved!")
        msg.setText("Your todo list has been saved!")
        msg.setIcon(QMessageBox.Information)
        x = msg.exec_()


    def retranslateUi(self, MainWindow):
        _translate = QtCore.QCoreApplication.translate
        MainWindow.setWindowTitle(_translate("MainWindow", "To do list"))
        self.add_pushButton.setText(_translate("MainWindow", "Add item"))
        self.delete_pushButton_2.setText(_translate("MainWindow", "Delete from list"))
        self.clearlist_pushButton_3.setText(_translate("MainWindow", "Clear list"))
        self.save_pushButton.setText(_translate("MainWindow", "Save"))


if __name__ == "__main__":
    import sys
    app = QtWidgets.QApplication(sys.argv)
    MainWindow = QtWidgets.QMainWindow()
    ui = Ui_MainWindow()
    ui.setupUi(MainWindow)
    MainWindow.show()
    sys.exit(app.exec_())
