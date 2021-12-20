
## Entity Framework notes

### Relationships

#### One-to-Many

```cs
public class Student {
   public int ID { get; set; }
   public string LastName { get; set; }
   public string FirstMidName { get; set; }
   public DateTime EnrollmentDate { get; set; }
	
   public virtual ICollection<Enrollment> Enrollments { get; set; }
}

public class Enrollment {

   public int EnrollmentID { get; set; }
   public int CourseID { get; set; }
   public int StudentID { get; set; }
	
   public Grade? Grade { get; set; }
   public virtual Course Course { get; set; }
   public virtual Student Student { get; set; }
}
```
#### Many-to-Many

```cs
public class Course {
   [DatabaseGenerated(DatabaseGeneratedOption.None)]
	
   public int CourseID { get; set; }
   public string Title { get; set; }
	
   public int Credits { get; set; } 
   public virtual ICollection<Enrollment> Enrollments { get; set; }
}
```

#### One-to-One

```cs
public class Student {
   public int ID { get; set; }
   public string LastName { get; set; }
   public string FirstMidName { get; set; }
   public DateTime EnrollmentDate { get; set; }
	
   public virtual ICollection<Enrollment> Enrollments { get; set; }
   public virtual StudentProfile StudentProfile { get; set; }
}

public class StudentProfile {

   public StudentProfile() {}
   public int ID { get; set; }
   public string Email { get; set; }
   public string Password { get; set; }
	
   public virtual Student Student { get; set; }
}
```

### Entity Framework - Lifecycle


