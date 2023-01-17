# CTF-Write-Up
                            * Tuyển thành viên ban chuyên môn KCSC - Write-Up
                            
                            
1) The Doki Club: https://bcm.kcslab.asia/challenges#The%20Doki%20Club-16
* Hướng giải: Đầu tiên tải file Dokiclub.zip, sau đó giải nén vào thư mục mới Dokiclub. Tiếp theo, vào thư mục DokiClub-1.0-win.
        * ![image](https://user-images.githubusercontent.com/122852491/212831009-0aa02bcc-e686-4553-bdb6-f0d6294eefab.png)

    Sau đó chạy file DokiClub.exe.
    
    Sau 1 loạt câu hỏi của nhân vật trong game thì mục đích chính là tìm ra đáp án của file question.txt rồi viết đáp án ra 1 file answer.txt
    
    ![image](https://user-images.githubusercontent.com/122852491/212831781-e116ac8e-2830-4566-aa69-0dca1fc3a65c.png)
    
                                                                File question.txt
                                                                
    Ở đây chúng ta phải hiểu được nội dung của đoạn code Assembly này. Do em mới tiếp xúc với CTF tầm 2 tuần nên cũng chưa hiểu nhiều về Assembly. Do vậy em lên google nghiên cứu một tí, và hiểu sơ qua đoạn code:
    
                    mov eax, 0x15  # gán giá trị 21(0x15) vào thanh ghi eax
                    mov ebx, 0x10  # gán giá trị 16(0x10) vào thanh ghi ebx
                    div ebx        # lấy giá trị lưu trong thanh eax(21) chia lấy phần nguyên với giá trị trong thanh ebx(15) = 1 rồi lưu vào thanh eax
                    mov ecx, 0x9   # gán giá trị 9(0x9) vào thanh ecx
                    sub ebx, 0x4   # giống như trong Python: ebx -= 0x4(4), lấy giá trị đang lưu trong ebx trừ đi 4 rồi lại lưu vào ebx, ebx lúc này = 12
                    add eax, 1     # tương tự như trên, eax += 1, eax lúc này = 2
                    mul ecx        # lấy giá trị trong eax nhân với giá trị trong ecx rồi lưu vào eax, eax lúc này = 2*9 = 18
                    add eax, ebx   # eax += ebx, vậy giá trị trong eax lúc này = 30
    
    Giờ em tạo 1 file answer.txt rồi ghi kết quả eax vào, rồi chạy lại file DokiClub.exe, thì nó tạo thêm 1 file mới Memory
    
    E thử mở Memory = Notepad, thì nó hiện ra như sau: 
    ![image](https://user-images.githubusercontent.com/122852491/212835726-fa0b5c44-b50a-4998-9491-280842bb8399.png)

    
    Đến đây thì chưa thể kết luận đây là flag được, e tiếp tục quay lại mò mầm những phần khác trong thư mục DokiClub-1.0-win. E vào game folder thì nó hiện ra 2 file khả nghi, archive.rpa và script_version.txt:
    
    ![image](https://user-images.githubusercontent.com/122852491/212836113-9cae066a-bb06-4e9f-9ca4-13aedba8540f.png)


    Bấm vào script_version.txt, nó chỉ hiện: (8, 0 ,3). Như vậy chưa rút ra được gì, tạm thời để đó.
    
    Còn lại file archive.rpa, e cũng thử đọc = Notepad.
    
    E thử Ctrl + F với từ khóa "KCSC", hòng tìm được thông tin gì về flag, kết quả cũng chẳng khả quan mấy, vẫn chỉ là cái "KCSC{Th3_C" giống như trong file Memory
    
    File archive.rpa dù bị mã hóa nhưng không hoàn toàn(hoặc có thể e hiểu sai :))), nhưng đọc = Notepad thì vẫn có vài đoạn code Python có thể đọc được. Vì vậy, e quyết định lăn xuống dưới để xem có tìm được gì nữa không, thì đến khúc này:
    
    ![image](https://user-images.githubusercontent.com/122852491/212837819-f929464a-8ce0-4730-9852-cfe25ec470ba.png)
    
    Ở dòng mainCharacter cuối cùng, dù 1 số chữ bị lỗi phông hay vấn đề gì đó(e không biết nói chính xác là gì), nhưng vẫn có thể dịch được ý của nó là: "Hãy giải mã file này để lấy flag". Thêm vào đó, ở dưới dòng mainCharacter cuối cùng có 1 đoạn code Python, và trong đó có đề cập đến 1 file tên là: "jour_pho_lac_iS_Here.txt", đại khái là "your flag is here". Cùng với đó, là 1 mảng China với những số viết dưới dạng hệ 16 và 1 hàm bytearray. Vì vậy, e copy mảng China rồi đưa vào Idle chạy thử, và đoán xem:
    
    ![image](https://user-images.githubusercontent.com/122852491/212839296-df8f0068-f7d7-4c92-8fcf-6d646dc6002c.png)


 Vì flag có định dạng là KCSC{...}, và bytearray trả về byte của array đó, đó là lý do tại sao lại có thêm đoạn b'\xfe\xff& ở trước
 
 * Flag:  KCSC{W3llC0m3_T0_Our_D0k1_d0ki_Club!!!}


2) Find me: https://bcm.kcslab.asia/challenges#Find%20me-14
    * Hướng giải: Theo description, "Find me in C disk". Đầu tiên, e tải file source.exe về vào ổ C. 
    
    ![image](https://user-images.githubusercontent.com/122852491/212840636-a1998b24-bea1-4215-9569-67c350e6782b.png)
    
    Chạy thử file source.exe, kết quả nó vừa hiện lên màn hình cmd thì tắt. Nếu nó chỉ như vậy thì không thể tìm được flag, vì vậy e nghĩ chương trình vừa chạy đã sinh ra 1 file gì đó vào 1 thư mục nào đó trong lúc chạy mà mình không biết.
    
    Vì description là "Find me in C disk", vì vậy e quyết định sẽ vào từng folder mặc định trong ổ C để tìm(vì không bao giờ chúng ta không tải 1 phần mềm gì về máy cả, mà thường thì mặc định program đó sẽ lưu trong ổ C, vì vậy người ra đề sẽ không thể giấu flag vào trong những folder chứa program đó được, vì mỗi máy tính mỗi người dùng khác nhau, vì vậy chỉ có thể giấu flag trong những folder mặc định).
    
    Sau 1 hồi rà soát, e vào folder Temp trong ổ C, thì thấy 1 file .txt có tên hơi lạ: 
    
    ![image](https://user-images.githubusercontent.com/122852491/212842756-4b07a833-944a-49d1-b923-13b3277652e6.png)

    
    Bấm vào, và điều gì đến cũng phải đến: 
    
    ![image](https://user-images.githubusercontent.com/122852491/212842260-38be58e8-1eb2-4f13-bb63-3490d5fa6ffd.png)
    
    * Flag: KCSC{T3t_n4y_4nh_kh0ng_th3m_d0t_ph4o_V1_ti3ng_cu0i_3m_r0n_r4_l0ng_4nh_r0i!}







            

