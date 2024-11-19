COLLECTIONS:
users
     {
  "_id": "ObjectId",
  "name": "String",
  "email": "String",
  "mentors": ["mentor_id"],
  "codekata_problems_solved": "Number",
  "attendance": [
    {
      "date": "Date",
      "status": "String" // "Present" or "Absent"
    }
  ],
  "tasks_submitted": [
    {
      "task_id": "ObjectId",
      "submitted": "Boolean"
    }
  ]
}
codekata


             {
  "_id": "ObjectId",
  "user_id": "ObjectId",
  "problems_solved": "Number"
}
Attendance 


          {
  "_id": "ObjectId",
  "user_id": "ObjectId",
  "date": "Date",
  "status": "String" // "Present" or "Absent"
}
Topics


     {
  "_id": "ObjectId",
  "name": "String",
  "date": "Date"
}
Tasks

         {
  "_id": "ObjectId",
  "name": "String",
  "topic_id": "ObjectId",
  "date": "Date"
}
CompanyDrives


          {
  "_id": "ObjectId",
  "company_name": "String",
  "date": "Date",
  "students_appeared": ["user_id"]
}
mentors


       {
  "_id": "ObjectId",
  "name": "String",
  "mentees": ["user_id"]
}



 1. Find all the topics and tasks which are taught in the month of October.
 db.topics.aggregate([
  {
    $match: {
      date: {
        $gte: new ISODate("2020-10-01"),
        $lt: new ISODate("2020-11-01")
      }
    }
  },
  {
    $lookup: {
      from: "tasks",
      localField: "_id",
      foreignField: "topic_id",
      as: "related_tasks"
    }
  }
]);



2. Find all the company drives which appeared between 15-Oct-2020 and 31-Oct-2020.
db.company_drives.find({
  date: {
    $gte: new ISODate("2020-10-15"),
    $lte: new ISODate("2020-10-31")
  }
});



3. Find all the company drives and students who appeared for the placement.
db.company_drives.aggregate([
  {
    $lookup: {
      from: "users",
      localField: "students_appeared",
      foreignField: "_id",
      as: "students_details"
    }
  }
]);



4. Find the number of problems solved by the user in CodeKata.
db.codekata.aggregate([
  {
    $group: {
      _id: "$user_id",
      total_problems_solved: { $sum: "$problems_solved" }
    }
  }
]);



5. Find all the mentors with mentee counts greater than 15.
db.mentors.find({
  $expr: { $gt: [{ $size: "$mentees" }, 15] }
});



6. Find the number of users who are absent and tasks are not submitted between 15-Oct-2020 and 31-Oct-2020.
db.users.aggregate([
  {
    $match: {
      "attendance": {
        $elemMatch: {
          date: { $gte: new ISODate("2020-10-15"), $lte: new ISODate("2020-10-31") },
          status: "Absent"
        }
      },
      "tasks_submitted": {
        $elemMatch: {
          submitted: false,
          date: { $gte: new ISODate("2020-10-15"), $lte: new ISODate("2020-10-31") }
        }
      }
    }
  },
  {
    $count: "absent_and_not_submitted"
  }
]);


![alt text](<Screenshot 2024-11-18 170052.png>)
![alt text](<Screenshot 2024-11-18 170106.png>)
![alt text](<Screenshot 2024-11-18 170118.png>)
![alt text](<Screenshot 2024-11-18 170139.png>)
![alt text](<Screenshot 2024-11-18 170205.png>)
![alt text](<Screenshot 2024-11-18 170205-1.png>)