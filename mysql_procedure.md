delimiter //
CREATE DEFINER=`root`@`%` PROCEDURE `signin_data`()
begin  
    declare v int default 0;  
    while v < 100000000
		do  
        insert into tblUserSignin  
        values (NULL, 5000000000 + v, ceil(rand()*100000000), ceil(rand()*100000000), '{"1":1,"2":1,"3":1,"14":1,"15":1,"23":1,"35": 1,"100":1,"101":1}', 1234123);  
         set v = v + 1;  
    end while;  
end//

delimiter ;

show procedure status;
show create procedure signin_data;
